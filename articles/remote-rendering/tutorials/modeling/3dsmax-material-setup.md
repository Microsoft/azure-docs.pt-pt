---
title: Configurar materiais PBR em 3ds Max
description: Explica como configurar materiais de renderização baseados fisicamente em 3ds Max e exportá-los para o formato FBX.
author: FlorianBorn71
ms.author: flborn
ms.date: 06/16/2020
ms.topic: tutorial
ms.openlocfilehash: 12407d6344c69c747230e9db6fa4d53b4520dc82
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91331816"
---
# <a name="tutorial-set-up-physically-based-rendering-materials-in-3ds-max"></a>Tutorial: Configurar materiais de renderização fisicamente em 3ds Max

## <a name="overview"></a>Descrição geral
Neste tutorial, irá aprender a:

>[!div class="checklist"]
>
> * Atribua materiais com iluminação avançada a objetos numa cena.
> * Manuseie a instalação de objetos e materiais.
> * Exporte uma cena para o formato FBX e selecione opções importantes.

> [!Note]
> O procedimento descrito neste tutorial funciona em 3ds Max 2019 e 3ds Max 2020.
> Uma mudança na forma como o 3ds Max 2021 exporta mapas de colisões significa que os mapas normais não serão encontrados pelo serviço de conversão se essa versão for utilizada.

Criar [materiais de renderização física (PBR)](../../overview/features/pbr-materials.md) em 3ds Max é uma tarefa simples. É semelhante em muitas formas à configuração do PBR em outras aplicações de criação de conteúdo como a Maya. Este tutorial é um guia para a configuração básica do sombreador PBR e exportação de FBX para projetos de renderização remota Azure.

A cena da amostra neste tutorial contém uma série de objetos de caixa de polígono. São-lhes atribuídos materiais diferentes, como madeira, metal, metal pintado, plástico e borracha. Em termos gerais, cada material contém todas ou a maioria das seguintes texturas:

* **Albedo,** que é o mapa de cores do material e também é chamado **de Difusão** e **BaseColor.**
* **Metalidade**, que determina se um material é metálico e quais as partes metálicas. 
* **Aspereza,** que determina o quão áspera ou lisa é uma superfície.
Também afeta a nitidez ou desfocagem dos reflexos e destaques numa superfície.
* **Normal,** que adiciona detalhes a uma superfície sem adicionar mais polígonos. Exemplos de detalhes são caroços e amassados numa superfície metálica ou grãos em madeira.
* **Oclusão ambiente,** que é usado para adicionar sombras suaves e sombras de contacto a um modelo. É um mapa em tons de cinza que indica quais as áreas do modelo que recebem iluminação completa (branca) ou sombra completa (preto).

## <a name="prepare-the-scene"></a>Preparar a cena
Em 3ds Max, o processo de criação de um material PBR é o seguinte.

Para começar, vamos criar uma série de objetos de caixa, cada um dos quais representa um tipo diferente de material.

>[!TIP]
>Vale a pena notar antes de começar a criar ativos para renderização remota que usa contadores para medição.  
>
>Então é uma boa ideia definir as unidades do sistema da sua cena em metros. Também é uma boa ideia definir **unidades** para metros nas definições de exportação FBX quando exporta uma cena.

A imagem que se segue mostra os passos para definir as unidades do sistema em 3ds Max. 

1. No menu principal, aceda à **configuração de**  >  unidades de**configuração de**  >  **unidades de configuração de unidades.** Na **Escala de Unidade do Sistema**, selecione **Meters**: ![ Screenshot que mostra como definir unidades do sistema.](media/3dsmax/system-units.jpg)

1. Podemos agora começar a criar os modelos. Na cena da amostra, vamos criar vários objetos de caixa, cada um representando um tipo de material diferente. Por exemplo, metal, borracha e plástico. 

   >[!TIP]
   >Quando se cria ativos, é uma boa prática nomeá-los adequadamente à medida que avançamos. Isto torna-os mais fáceis de encontrar mais tarde se a cena contiver muitos objetos.

1. Mude o nome dos objetos, como mostra a seguinte imagem: 

   ![Screenshot que mostra como renomear objetos.](media/3dsmax/rename-objects.jpg)

## <a name="assign-materials"></a>Atribuir materiais

Agora que temos alguns objetos na nossa cena, neste caso uma série de cubos, podemos começar a configuração do PBR:

1. Na barra de ferramentas principal, selecione o ícone **do Editor de Material** como mostrado na imagem seguinte. Também pode selecionar **M** no seu teclado para abrir o editor. O Editor de Material tem dois modos que pode selecionar na lista de Modos: Modo **Editor de Material Compacto** e modo **Editor de Material de Ardósia.** **Modes** Como esta cena é relativamente simples, vamos usar o modo compacto.

1. No Editor de Materiais, verás várias esferas. Estas esferas são os materiais. Vamos atribuir um destes materiais a cada objeto (cada caixa) na cena. Para atribuir os materiais, selecione primeiro um dos objetos no porto principal. Em seguida, selecione a primeira esfera no Editor de Material. Depois de atribuído a um objeto, o material selecionado será destacado, como mostra a imagem seguinte.

1. Selecione **Atribuir Material à Seleção,** como mostrado. O material é agora atribuído ao objeto selecionado.

   ![Screenshot que mostra como atribuir materiais.](media/3dsmax/assign-material.jpg)

    No Editor de Materiais, pode escolher entre uma vasta seleção de tipos de materiais, dependendo das suas necessidades. Normalmente, o tipo de material é definido como **Standard** por padrão. Este material é um material básico que não é adequado para a configuração do PBR. Por isso, temos de mudar o tipo de material para um material PBR. Material Físico é o material 3ds Max preferido para projetos de renderização remota Azure.

1. No Editor de Materiais, selecione o **separador Standard.** No **Navegador Material/Mapa,** selecione **Material Físico**. Esta ação converte o material **Standard** atribuído a um Material Físico PBR.

   ![Screenshot que mostra como mudar o material.](media/3dsmax/physical-material.jpg)

    No Editor de Materiais, vê-se agora as propriedades do Material Físico, como mostra a imagem seguinte. Pode agora começar a atribuir texturas ao ativo.

   ![Screenshot que mostra a lista de texturas.](media/3dsmax/textures-list.jpg)

Como pode ver, há uma grande variedade de mapas e texturas que pode adicionar ao material. Para este tutorial, usamos apenas cinco ranhuras de textura no material.

>[!TIP]
>É uma boa prática nomear os seus materiais de forma apropriada, como mostra a imagem anterior.

A forma como gera as suas texturas pode variar de acordo com a preferência ou utilização. Por exemplo, pode querer usar texturas de inclinação que podem ser aplicadas a qualquer ativo. Ou você pode precisar de partes específicas de um projeto ou ativo para ter seus próprios conjuntos personalizados de texturas. Você pode querer usar texturas genéricas de inclinação que você pode obter on-line. Você também pode criá-los em apps como Photoshop, Quixel Suite e Substance Suite.

Antes de começarmos a atribuir texturas, teremos de considerar as coordenadas de textura do ativo (UVW). É uma boa prática quando se aplicam quaisquer texturas a um modelo para garantir que o modelo está desembrulhado. (As texturas não serão exibidas corretamente sem o desembrulhamento UV adequado.) É especialmente importante para os nossos propósitos porque queremos usar um mapa de Oclusão Ambiente (AO) no nosso modelo. Ao contrário de Stingray Shader em Maya, material físico em 3ds Max não tem uma ranhura de textura AO dedicada. Vamos aplicar o mapa AO a outra ranhura. Para permitir que seja usado separadamente das outras texturas (texturas de inclinação, por exemplo), vamos atribuí-lo um canal de mapa UVW por si só. 

Começaremos por atribuir um modificador UVW desembrulhar para o modelo, como mostra a imagem seguinte. 

- No editor de propriedades de objetos selecionados, selecione a lista de modificadores. Na lista de drop-down que aparece, desloque-se para baixo e selecione **Desembrulhar UVW**. Esta ação aplica um modificador UVW desembrulhador ao ativo.
![Screenshot que mostra como selecionar Desembrulhar UVW.](media/3dsmax/unwrap-modifier.jpg)

  O canal do mapa está definido para 1. Normalmente, você vai fazer o desembrulho principal no canal de mapa 1. Neste caso, o objeto foi desembrulhado sem coordenadas de textura sobrepostas (UV).
![Screenshot que mostra coordenadas de textura desembrulhadas (UVW).](media/3dsmax/unwrapped-uvw.jpg)

O próximo passo é criar um segundo canal de mapa UV.

1. Feche o editor uv se estiver aberto. Na secção **Channel** do menu **EditUVs,** altere o número do canal para **2**. O canal 2 do mapa é o canal esperado para mapas AO. 

1. Na caixa de diálogo de aviso de alteração do **canal,** pode **mover** os UVs existentes no canal 1 para o novo canal 2 ou **abandonar** os UVs existentes que criarão um novo desembrulhável UV automaticamente. Selecione **Abandon** apenas se planeia criar um novo desembrulhável UV para o mapa AO que difere dos UVs no canal de mapa 1. (Por exemplo, se quiser utilizar texturas de inclinação no canal 1.) Neste tutorial, vamos mover os UVs do canal um para o canal 2 porque não precisamos de editar o novo canal UV.

   >[!NOTE]
   >Mesmo que tenha copiado (movido) o desembrulho UV do canal 1 do mapa para o canal 2 do mapa, pode fazer todas as edições necessárias para os novos UVs do canal sem afetar o canal de mapa original.

   ![Screenshot que mostra o Aviso de Mudança de Canal.](media/3dsmax/channel-change.jpg)

Agora que criámos o novo canal de mapas, podemos voltar ao Material Físico no Editor de Materiais e começar a adicionar as nossas texturas a ele. Primeiro vamos adicionar o mapa AO porque há outro passo para permitir que funcione corretamente. Depois do mapa AO estar ligado ao nosso material, precisamos configurá-lo para usar o canal de mapa 2.

Como já foi notado, não há uma ranhura dedicada para mapas AO no material físico 3ds Max. Em vez disso, aplicaremos o mapa AO na ranhura **difusa de Aspersão.**

1. Na lista de **Mapas Genéricos** do Material Físico, selecione a ranhura **No Map** ao lado **da Rugosidade Difusa** e carregue o seu mapa AO.

1. Nas propriedades de texturas AO, o canal de mapa é definido para **1** por padrão. Altere este valor para **2**. Esta ação completa os passos necessários para adicionar o seu mapa AO.

   >[!IMPORTANT]
   >Este é um passo importante, especialmente se os seus UVs no canal 2 forem diferentes dos do canal 1, porque o AO não mapeia corretamente se o canal errado for selecionado.

   ![Screenshot que mostra como atribuir um mapa AO.](media/3dsmax/assign-ao-map.jpg)

Vamos agora atribuir o mapa normal ao material PBR. Esta ação difere um pouco do processo em Maya. O mapa normal não é aplicado diretamente na ranhura do mapa de colisões. (Não há uma ranhura normal no material físico 3ds Max.) Em vez disso, adicione o mapa normal a um modificador de mapas normal, que por si só está ligado à ranhura normal.

1. Na secção **Mapas Especiais** das propriedades do Material Físico (no Editor de Material), selecione a ranhura **No Map** ao lado **do Bump Map**. 

1. No **navegador material/mapa,** localize e selecione **O Bump Normal**. Esta ação adiciona um **modificador de colisão normal** ao material.

1. No **modificador de colisão normal,** selecione **No Map** ao lado **do Normal**. Localize e carregue o seu mapa normal.

1. Certifique-se de que o método está definido para **Tangent**. (Deve ser, por defeito.) Se necessário, **alternar Flip Green (Y)**.

   ![Screenshot que mostra como selecionar O Bump Normal. ](media/3dsmax/normal-bump.jpg)
    ![ Screenshot que mostra carregar o mapa normal.](media/3dsmax/load-normal-map.jpg)

Com o mapa normal corretamente atribuído, podemos atribuir as restantes texturas para completar a configuração do Material Físico. Este processo é simples. Não há configurações especiais a considerar. A imagem a seguir mostra o conjunto completo de texturas atribuídas ao material: 

![Screenshot que mostra todo o conjunto de texturas atribuídas ao material.](media/3dsmax/all-textures.jpg)

Agora que os materiais PBR são criados e configurados, vale a pena pensar em instalar objetos na cena. Exemplo de objetos semelhantes na cena, como porcas, parafusos, parafusos e anilhas. Quaisquer objetos iguais podem gerar poupanças significativas em termos de tamanho do ficheiro. Exemplos de um objeto mestre podem ter a sua própria escala, rotação e transformações, para que você possa colocá-los como necessário na sua cena. Em 3ds Max, o processo de instancing é simples.

1. No porto principal, selecione o objeto ou objetos que pretende exportar.

1. Segure a tecla **Shift** e arraste os ativos para cima utilizando a ferramenta de transformação (mover). 

1. Na caixa de diálogo **Opções Clone,** defina **o objeto** para **a instância** e, em seguida, selecione **OK**:

   ![Screenshot da caixa de diálogo Opções Clone.](media/3dsmax/instance-object.jpg)

Esta ação cria uma instância do seu objeto que pode mover, rodar ou escalar independentemente do seu progenitor e de outros casos desse progenitor.

>[!IMPORTANT]
>Quaisquer alterações que faça a uma instância enquanto estiver no modo sub-objeto são transmitidas a todas as instâncias do objeto. Por isso, se estiver a trabalhar com componentes de um objeto, como vértices e rostos de polígono, certifique-se de que quer quaisquer alterações que faça para afetar todas as instâncias. Lembre-se que qualquer objeto de instância pode ser transformado num objeto único a qualquer momento. 

>[!TIP]
>Ao entrar na sua cena, é uma boa ideia criar casos à medida que avança. Substituir cópias por objetos posteriores é difícil. 

Uma última coisa a considerar antes de passarmos ao processo de exportação é como você pode querer embalar a sua cena/ativo para compartilhar. Idealmente, se passar o ativo para clientes ou membros da equipa, você vai querer que eles sejam capazes de abrir e ver o ativo como deve ser visto com um mínimo de alarido. Por isso, é importante manter os caminhos de textura do seu ativo em relação ao ficheiro da cena. Se os caminhos de textura do seu ativo apontarem para uma unidade local ou caminho/local absoluto, eles não carregarão na cena se forem abertos em um computador diferente, mesmo que o ficheiro .max esteja na mesma pasta que as texturas. Tornar os caminhos de textura relativos em 3ds Max resolve este problema e é bastante simples.

1. Na barra de ferramentas **File**principal, aceda ao  >  **Reference**  >  **Toggle de Rastreio de Ativos**de Referência de Ficheiros . 

1. Na janela de Rastreio de Ativos, você verá todas ou a maioria das texturas que você aplicou aos seus materiais PBR listados na coluna **Maps/Shaders.**

1. Ao lado deles, na coluna **Caminho Completo,** você verá o caminho da localização das suas texturas, muito provavelmente o caminho da sua localização na sua máquina local.

1. Finalmente, verá uma coluna chamada **Status**. Esta coluna indica se uma determinada textura foi localizada e aplicada à sua cena. Sinaliza a textura com um destes termos: **Ok,** **Found,** ou **File Missing**. Os dois primeiros indicam que o ficheiro foi encontrado e carregado. O último obviamente significa que o localizador falhou em localizar o ficheiro.
 
   ![Screenshot que mostra a janela de rastreio de ativos.](media/3dsmax/texture-paths.jpg)

Pode notar que nem todas as suas texturas estão listadas na janela de rastreio de ativos quando a abrir pela primeira vez. Isto não é nada com que se preocupar. Correr pelo processo de descoberta de caminhos uma ou duas vezes geralmente encontra todas as texturas de uma cena. O processo de descoberta de caminhos é o seguinte: 

1. Na janela de rastreio de ativos, mantenha a tecla **Shift** e selecione a textura superior na lista **Maps/Shaders** e, continuando a segurar **Shift,** selecione a última textura da lista. Esta ação seleciona todas as texturas da lista. As texturas selecionadas são destacadas em azul. (Ver a imagem anterior.)

1. Clique com o botão direito na seleção e selecione **set Path**.

1. Na caixa Especificar Caminho **do Ativo,** selecione o caminho local para as suas texturas e substitua-o por `.\` .  Selecione **OK**. 

    A janela de rastreio de ativos atualizar-se-á conforme mostrado na imagem a seguir. Esta atualização pode demorar algum tempo, dependendo de quantas texturas existem na sua cena e da sua grande quantidade de cena.
![Screensthot que mostra a janela de rastreio de ativos atualizada.](media/3dsmax/resolve-textures.jpg)

Note que a coluna **Caminho Completo** está agora em branco. Isto significa que a cena já não está à procura das texturas relevantes num local específico (absoluto). Irá sempre encontrá-los enquanto o ficheiro .max ou o ficheiro FBX relacionado estiverem na mesma pasta que as texturas. 

>[!NOTE]
>Você pode ter que repetir este processo algumas vezes para encontrar e resolver todas as texturas e caminhos. Isto não é nada com que se preocupar. Basta repetir o processo até que todos os ativos relevantes sejam contabilizados. Em alguns casos, alguns ficheiros não serão encontrados. Nesse caso, basta selecionar todos os ativos da lista e, em seguida, selecionar **Remover Caminhos Desaparecidos**. (Ver a imagem anterior.)

## <a name="fbx-export"></a>Exportação de FBX

Agora que fizemos os caminhos de textura relativos, podemos passar para a exportação de FBX. Mais uma vez, o processo é simples, e pode fazê-lo de várias maneiras. 

>[!TIP]
>A menos que queira exportar toda a sua cena, é uma boa ideia selecionar para exportar apenas os ativos de que precisa. Em cenas intensivas de recursos, a exportação pode demorar muito tempo.
>
>Se já utilizou modificadores como o Turbosmooth ou o Open SubDiv, é uma boa ideia desabar antes da exportação, porque podem causar problemas durante a exportação. Certifique-se de salvar a sua cena antes de as desmoronar. 

1. No local, selecione os bens que pretende exportar. Na barra de ferramentas principal, vá ao **Arquivo**  >  **Exportação**  >  **De Exportação Selecionado.**

1. Na caixa de diálogo **Select File to Export,** digite ou selecione um nome de ficheiro de saída. Na lista **de tipo Save,** selecione **Autodesk (*.fbx)**. Esta ação abre a janela FBX Export.

  >[!IMPORTANT] 
  >Se criou casos na sua cena, é importante selecionar **Instâncias de Preservação** nas definições de exportação de FBX. 

  ![Screenshot que mostra como exportar para FBX.](media/3dsmax/fbx-export.jpg)

  Lembre-se, há algumas maneiras de exportar o ficheiro. Se a intenção for partilhar o FBX juntamente com os seus ficheiros de textura numa pasta/diretório, as definições mostradas na imagem seguinte devem funcionar bem. 

   Se preferir não partilhar grandes pastas/diretórios de texturas juntamente com o FBX, pode optar por incorporar as texturas no FBX. Se incorporar as texturas, todo o ativo, incluindo texturas, é adicionado a um único FBX. Ao fazê-lo, combina a sua exportação num único ativo, mas o ficheiro FBX será consideravelmente maior como resultado.

   >[!IMPORTANT]
   >Se o ficheiro FBX resultante for maior do que 2,4 GB, a versão mínima especificada nas definições de exportação FBX deve ser 2016 ou mais tarde. (Ver a imagem anterior.) As versões mais recentes têm 64 bits de suporte, por isso suportam ficheiros maiores.

1. Se pretender exportar a cena com texturas incluídas, na janela *FBX Export, selecione **Embed Media**. 

1. Selecione o resto das suas definições e, em seguida, selecione **OK**:

    ![Screenshot que mostra as definições de exportação FBX.](media/3dsmax/fbx-settings.jpg)


   Quando exporta para FBX durante a utilização de um material físico, provavelmente verá o seguinte aviso depois de selecionar **OK** na janela FBX Export: 

   ![Screenshot que mostra o aviso falhado da exportação de material.](media/3dsmax/export-warnings.jpg)

   Este aviso diz-lhe que os materiais exportados podem não ser compatíveis com outros pacotes de software. Como o Material Físico é compatível com a renderização remota Azure, não precisa de se preocupar com este aviso. 

1. Selecione **OK** para concluir o processo e feche a janela.

## <a name="conclusion"></a>Conclusão

Em geral, este tipo de material parece mais realista porque é baseado na física da luz do mundo real. Cria um efeito imersivo adicional para que a cena pareça existir no mundo real.

## <a name="next-steps"></a>Passos seguintes

Agora sabe como configurar materiais com iluminação avançada para objetos numa cena. Também sabe exportar objetos para o formato FBX, que é suportado pela Azure Remote Rendering. O próximo passo é converter o ficheiro FBX e visualizá-lo em Renderização Remota Azure.

>[!div class="nextstepaction"]
>[Quickstart: Converter um modelo para renderização](../../quickstarts\convert-model.md)
