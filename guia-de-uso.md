# Rackview 3D — Guia de uso

Visual 3D de racks para o Power BI. Cada posição de estoque vira um palete colorido dentro da
estrutura do rack, e você navega com o mouse. Este guia ensina a usar; não é preciso programar.

**Sumário**

1. [Primeiros passos](#1-primeiros-passos)
2. [Posicionar: grade automática ou planta](#2-posicionar-grade-automática-ou-planta)
3. [Galpões: paredes, piso e altura](#3-galpões-paredes-piso-e-altura)
4. [Zonas](#4-zonas)
5. [Cores e legenda](#5-cores-e-legenda)
6. [Filtrar sem perder a estrutura (campo Exibir)](#6-filtrar-sem-perder-a-estrutura-campo-exibir)
7. [Navegar no 3D](#7-navegar-no-3d)
8. [Painel de formatação, resumo](#8-painel-de-formatação-resumo)
9. [Limites e problemas comuns](#9-limites-e-problemas-comuns)

---

## 1. Primeiros passos

**Uma linha por posição.** Cada combinação Rua + Rack + Nível deve aparecer **uma vez**. Se a base tem
várias linhas por posição (vários materiais no mesmo palete), agregue antes no Power Query.

| Rua | Rack | Nível | Quantidade | Contado |
|---|---|---|---|---|
| 1 | 01 | A | 36 | Sim |
| 1 | 01 | B | 0 | Não |
| 1 | 02 | A | 51 | Sim |

**Traga todas as posições do cadastro**, inclusive as vazias. O visual só desenha o que chega: se você
filtrar a base, a estrutura encolhe. Para filtrar sem isso, use o campo **Exibir** (seção 6).

**Arraste os campos para o visual:**

| Campo | Obrigatório | Para que serve |
|---|---|---|
| **Rua**, **Rack**, **Nível** | Sim | Onde cada palete fica |
| **Colorir por** | Não | Campo que define a cor (quantidade, contado, material…) |
| **Cor (hex)** | Não | Coluna com cores prontas `#RRGGBB`; sobrepõe a cor por valor |
| **Exibir** | Não | Medida ou coluna 0/1 que esconde paletes (seção 6) |
| **Coord X**, **Coord Y** | Não | Posição de cada rack na planta (seção 2) |
| **Galpão** | Não | Paredes e piso por galpão (seção 3) |
| **Altura do galpão** | Não | Altura dos racks de cada galpão (seção 3) |
| **Tooltip / detalhes** | Não | Campos que aparecem ao passar o mouse |

Use um só campo em cada slot, exceto em **Tooltip / detalhes**. Rua, Rack e Nível podem ser texto ou
número; linhas com algum deles vazio são ignoradas e contadas no aviso da legenda.

---

## 2. Posicionar: grade automática ou planta

O visual tem dois modos. O modo é escolhido pelos campos: **sem** Coord X/Y é grade automática; **com**
Coord X e Coord Y é planta.

### Grade automática (Rua, Rack e Nível)

O visual organiza tudo sozinho: as ruas ficam uma atrás da outra, o rack define a posição ao longo da
rua e o nível define a altura.

Opções em **Layout**:

| Opção | O que permite |
|---|---|
| Agrupar ruas em pares | Encosta as ruas de duas em duas, com corredor entre os pares |
| Pareamento das ruas | **Pela posição na lista** (1ª com 2ª…) ou **Pelo número da rua** (1 com 2, 3 com 4…) |
| Primeira rua é a 2ª do par | Corrige o par quando a primeira rua exibida é a segunda de um par físico |
| Pares começam em rua par | No pareamento por número, forma 2-3, 4-5… |
| Rótulos de rua | Mostra "Rua N" na ponta de cada rua |
| Ordem do nível | **Natural** (números antes de letras) ou **Personalizada** (você digita `A,B,C,1,2`) |
| Inverter níveis / racks / ruas | Troca a ordem de cada eixo (racks invertidos alinham as ruas no lado oposto) |

Em **Geometria**: **Altura do nível** (0,8 a 4) e **Largura do corredor** (1 a 12).

Cada rua tem o tamanho dos seus dados: uma rua com 12 racks e 3 níveis fica menor que uma com 45 racks e
8 níveis.

### Planta (Coord X e Coord Y)

Use quando os galpões têm formatos, orientações ou posições diferentes. Cada rack recebe uma coordenada
em um plano único, e o visual desenha exatamente nelas.

**Como preencher**

- Coloque **Coord X** e **Coord Y** (coluna ou medida) na tabela de posições. O valor é o do **rack** e
  se repete em cada nível dele.
- A unidade é **1 rack**: racks vizinhos têm coordenadas vizinhas (X=10, X=11…).
- **Corredores são vãos**: deixe uma ou mais coordenadas sem rack para abrir um corredor.
- **A rua pode ser horizontal ou vertical**: o visual percebe pela variação das coordenadas.
- Y aumenta para baixo na vista de cima. Se o desenho ficou espelhado, multiplique X (ou Y) por -1 nos
  dados.
- **Rua com duas fileiras** (o corredor no meio, numeração em U ou zigue-zague) funciona: basta dar a
  coordenada de cada rack.

**Opções que valem na planta**

| Opção | O que permite |
|---|---|
| Geometria → Tamanho da célula | Tamanho de cada rack no desenho (0,8 a 3) |
| Geometria → Altura do nível | Altura de cada nível (0,8 a 4) |
| Layout → Rótulos de rua | Liga e desliga os rótulos |
| Layout → Rótulo da rua | **Por corredor**: um rótulo por rua, no meio das fileiras. **Por fileira**: um em cada fileira |
| Layout → Ordem do nível / Inverter níveis | Iguais à grade automática |

Na planta **não valem** o pareamento, a largura do corredor nem inverter racks/ruas: as coordenadas já
definem isso.

**Avisos na legenda**

- *Rack sem coordenada:* não é desenhado.
- *Coordenadas divergentes:* o mesmo rack com mais de uma coordenada; vale a da primeira linha.
- *Sobreposição:* dois racks na mesma célula.

Uma rua com **um rack só** é desenhada na horizontal, porque não há como saber a direção.

---

## 3. Galpões: paredes, piso e altura

### Paredes e piso por galpão

Coloque o campo **Galpão** (com Coord X e Coord Y). O visual desenha uma **parede baixa** ao redor de
cada galpão e um **piso colorido** por galpão. Onde dois galpões se encostam, dividem a mesma parede.

Em **Galpões**:

| Opção | O que permite |
|---|---|
| Mostrar paredes / Cor da parede | Liga e desliga as paredes e escolhe a cor |
| Altura da parede | Altura das paredes (0,2 a 3) |
| Margem da parede | Folga entre os racks e a parede, em células (0 a 6). Aumente até dois galpões vizinhos dividirem a parede |
| Piso colorido por galpão | Liga e desliga o piso |

O contorno é sempre um **retângulo** ao redor dos racks do galpão.

### Altura do galpão

Ponha uma coluna com a altura de cada galpão (por exemplo 7, 9, 9) no campo **Altura do galpão**. O valor
pode repetir em todas as linhas do galpão.

- Todos os racks do galpão passam a ter **essa altura**, e a altura de cada nível se ajusta ao número de
  níveis. Assim, o galpão de 9 níveis fica com níveis baixos e o de 4 níveis com níveis altos, todos com
  a mesma altura total.
- Vale a unidade da cena (parecida com metros).
- Se a altura for pequena demais para os níveis, o visual usa o mínimo possível e avisa na legenda.
- Sem o campo, vale a **Altura do nível** do cartão Geometria.
- Sem o campo Galpão, o valor vale para todos os racks.

---

## 4. Zonas

Marca áreas que não são racks, como doca, devolução ou escritório. Só na **planta**.

1. Em **Zonas (máximo 20)**, ligue **Ativar zonas**.
2. Em **Número de zonas**, escolha quantas quer (até 20).
3. Preencha cada **Zona N**:
   - **Título:** aparece flutuando sobre a zona.
   - **X1, Y1** e **X2, Y2:** dois cantos opostos, nas mesmas coordenadas da planta. O retângulo cobre as
     células inteiras entre eles.
   - **Cor:** cor do piso e do contorno.
   - **Altura:** 0 desenha só o piso; acima de 0 vira um volume baixo, como uma doca elevada.
4. **Mostrar títulos das zonas** liga e desliga os títulos.

Se diminuir o número de zonas, os dados das escondidas ficam guardados. Zona sem título e com tudo zerado
é ignorada.

---

## 5. Cores e legenda

Coloque um campo em **Colorir por** e abra **Formato → Cores**. O painel se adapta ao tipo do campo.

| Modo | Quando usar | O que permite |
|---|---|---|
| **Automático** (padrão) | Sempre | Escolhe pelo tipo do campo: número vira gradiente; texto, sim/não e data viram categórico |
| **Categórico** | Poucos valores diferentes | Uma cor para cada valor. As cores ficam guardadas por valor. São até 30 valores; os demais ficam em cinza ("Outros") |
| **Gradiente** | Campo numérico | Cor mínima, máxima e, se quiser, média. Mínimo e máximo automáticos ou digitados. O zero conta como "sem valor" |
| **Faixas** | Campo numérico | De 2 a 6 faixas, cada uma com limite e cor. O limite entra na faixa; a última faixa é aberta |

Outras opções:

- **Sem valor:** cor de quem tem o campo vazio.
- **Palete padrão:** cor de todos quando não há "Colorir por".
- **Cor (hex):** se você calcula a cor no Power Query ou numa medida, o resultado `#RRGGBB` sobrepõe
  qualquer regra.
- **Ocultar valores:** lista de valores cujos paletes somem (a estrutura fica). Separe por vírgula:
  `Não, 0, (Sem valor)`. Para números, use `>30`, `<=2`. Com vírgula decimal, separe por ponto e vírgula.

**Legenda:** mostra cada valor ou faixa com a cor e a contagem (e o %, se quiser), considerando só o que
está visível. Em **Legenda** você escolhe mostrar, posição, fonte, contagem e %. Os avisos também
aparecem nela.

---

## 6. Filtrar sem perder a estrutura (campo Exibir)

Um slicer comum filtra a base e a estrutura dos racks encolhe. Para filtrar e **manter os racks
desenhados**, escondendo só os paletes, use o campo **Exibir**: o slicer alimenta uma medida que diz, por
posição, se ela aparece (1) ou não (0).

**Passo a passo (exemplo com Material)**

1. Crie uma **tabela só para o slicer**, sem relacionamento com a base (Modelagem → Nova tabela):

   ```dax
   SlicerMaterial = DISTINCT ( Posicoes[Material] )
   ```

2. Crie o slicer com o campo `SlicerMaterial[Material]`.
3. Crie a medida:

   ```dax
   Visivel =
   VAR ok =
       NOT ISFILTERED ( SlicerMaterial[Material] )
           || SELECTEDVALUE ( Posicoes[Material] ) IN VALUES ( SlicerMaterial[Material] )
   RETURN
       IF ( ok, 1, 0 )
   ```

4. Arraste `Visivel` para o campo **Exibir** do visual.

Sem seleção, tudo aparece. Com seleção, só os paletes escolhidos ficam visíveis.

**Vários filtros:** crie uma tabela e um slicer para cada filtro e combine na mesma medida com `&&`. O
palete só aparece se passar em todos.

**Posição com vários itens no mesmo texto** (por exemplo "ITEM1, ITEM2") e filtro por depósito:

```dax
Visivel =
VAR sep = ", "
VAR itensPos = sep & SELECTEDVALUE ( Posicoes[Itens na Posição] ) & sep
VAR okItem =
    NOT ISFILTERED ( SlicerItem[Item] )
        || COUNTROWS (
            FILTER (
                VALUES ( SlicerItem[Item] ),
                CONTAINSSTRING ( itensPos, sep & SlicerItem[Item] & sep )
            )
        ) > 0
VAR okDeposito =
    NOT ISFILTERED ( SlicerDeposito[Depósito] )
        || SELECTEDVALUE ( Posicoes[Depósito] ) IN VALUES ( SlicerDeposito[Depósito] )
RETURN
    IF ( okItem && okDeposito, 1, 0 )
```

O separador (`sep`) deve ser igual ao usado na concatenação. Ele evita que `ITEM1` case com `ITEM10`. Com
vários itens selecionados, a posição aparece se tiver **qualquer** um.

**O que o campo Exibir entende**

| Valor | Resultado |
|---|---|
| `1`, Verdadeiro, `Sim`, qualquer número diferente de zero | Visível |
| `0`, Falso, `Não`, texto vazio, vazio | Oculto |
| Campo Exibir sem nada | Tudo visível |

> A medida deve devolver `0` de forma explícita (`IF ( ok, 1, 0 )`). Se devolver vazio, o Power BI pode
> descartar a linha.

---

## 7. Navegar no 3D

| Ação | Como |
|---|---|
| Girar | Arrastar com o botão esquerdo |
| Aproximar / afastar | Roda do mouse |
| Mover a visão | Botão do meio, ou **Shift** + arrastar |
| Reenquadrar | Botão **Reenquadrar** (canto superior direito) |
| Ver detalhes | Passar o mouse sobre um palete |
| Selecionar | Clicar num palete; **Ctrl** + clique seleciona vários; clique no vazio limpa |
| Menu do Power BI | Botão direito |

Selecionar um palete filtra os outros visuais da página e esmaece o resto. A câmera **não mexe sozinha**
quando você filtra; para reenquadrar ao mudar as dimensões, ligue **Câmera → Reenquadrar ao mudar as
dimensões**.

---

## 8. Painel de formatação, resumo

| Cartão | O que tem |
|---|---|
| **Cores** | Modo, cores por valor/gradiente/faixas, Sem valor, Palete padrão, Ocultar valores |
| **Legenda** | Mostrar, posição, fonte, contagem, % |
| **Layout** | Pares, pareamento, rótulos de rua (e tipo na planta), ordem do nível, inverter |
| **Geometria** | Altura do nível, largura do corredor (grade) ou tamanho da célula (planta) |
| **Galpões** | Paredes, cor, altura, margem e piso por galpão (planta com campo Galpão) |
| **Zonas (máximo 20)** | Zonas com título, cantos, cor e altura (planta) |
| **Cena** | Fundo, piso (grade ou sólido) e sua cor, névoa, cor dos montantes e das longarinas |
| **Câmera** | Reenquadrar ao mudar as dimensões |
| **Tooltip** | Mostrar e incluir o campo "Colorir por" |
| **Desempenho** | Resolução, reduzir ao mover, mostrar longarinas, diagnóstico (fps) |

Em máquina fraca, comece por **Resolução máxima → Leve** e **Mostrar longarinas** desligado.

---

## 9. Limites e problemas comuns

- Chegam até **30.000 linhas**; acima disso a legenda avisa "dados truncados".
- Posições **duplicadas** usam a primeira linha e geram aviso.
- O visual foi testado com mais de 12.000 posições, roda offline e não carrega nada da internet.

**A estrutura encolhe quando uso um slicer.** O slicer está filtrando a base. Use a tabela desconectada
com o campo **Exibir** (seção 6).

**Nada some quando escolho no slicer.** Confira se a medida está no campo **Exibir** e se o slicer usa a
tabela desconectada.

**A câmera ficou longe ou perto demais.** Clique em **Reenquadrar**.

**O gradiente não aparece.** O campo em **Colorir por** precisa chegar como número.

**A planta ficou espelhada ou girada.** Multiplique X (ou Y) por -1 nos dados. Confira também se o X e o Y
de um galpão não estão trocados.

**Os racks viraram um bloco sem corredor.** Na planta os corredores são vãos nas coordenadas: deixe
linhas vazias entre os pares de ruas.

**Não aparecem paredes nem zonas.** Elas só existem na planta (Coord X e Coord Y). As paredes precisam
também do campo **Galpão**.

**Uma rua está formando par com a vizinha errada (grade automática).** Use **Pareamento → Pelo número da
rua**, e **Pares começam em rua par** se o par físico for 2-3, 4-5.

**Está pesado ao girar.** Veja **Desempenho**: resolução Leve e sem longarinas.

**Um valor novo apareceu em cinza.** Passou de 30 valores no modo categórico. Filtre, agrupe ou use
faixas.
