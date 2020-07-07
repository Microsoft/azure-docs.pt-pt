---
title: Configurar materiais de renderização fisicamente em 3DSMax
description: Explica como configurar materiais de renderização baseados fisicamente em 3DSMax e exportá-los para o formato FBX.
author: muxanickms
ms.author: misams
ms.date: 06/16/2020
ms.topic: tutorial
ms.openlocfilehash: df4be8963c93199f9fad23ab3f709f691e1da768
ms.sourcegitcommit: 9b5c20fb5e904684dc6dd9059d62429b52cb39bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85880117"
---
# <a name="tutorial-set-up-physically-based-rendering-materials-in-3d-studio-max"></a>Tutorial: Configurar materiais de renderização fisicamente baseados em Estúdio Max 3D

## <a name="overview"></a>Descrição Geral
Neste tutorial, ficará a saber como:

>[!div class="checklist"]
>
> * Atribua materiais com iluminação avançada a objetos na cena.
> * Manuseie a instalação de objetos e materiais.
> * Exporte uma cena para o formato FBX e selecione opções importantes.

Criar [materiais de renderização física (PBR)](../../overview/features/pbr-materials.md) em 3D Studio Max (3DSMax) é uma tarefa relativamente simples. É semelhante em muitas formas à configuração do PBR em outras aplicações de criação de conteúdo como a Maya. Este tutorial é um guia para a configuração básica do sombreador PBR e exportação de FBX para projetos de renderização remota Azure.

A cena da amostra neste tutorial contém uma série de objetos de caixa de polígono. São-lhes atribuídos diferentes materiais, como madeira, metal, metal pintado, plástico e borracha. Em termos gerais, cada material contém todas ou a maioria das seguintes texturas:

* **Albedo,** que é o mapa de cores do material e também é chamado **de Difusão** ou **BaseColor.**
* **Metalidade**, que determina se um material é metálico e quais as partes metálicas. 
* **Aspereza,** que determina o quão áspera ou lisa é uma superfície.
Também afeta a nitidez ou desfocagem dos reflexos e destaques numa superfície.
* **Normal,** que adiciona detalhes a uma superfície sem ter que adicionar mais polígonos. Exemplos de detalhes podem ser de acaso e amolgadelas numa superfície metálica ou grãos em madeira.
* **Oclusão ambiente,** que é usado para adicionar sombras suaves e sombras de contacto a um modelo. É um mapa em tons de cinza que indica quais as áreas de um modelo que recebem iluminação completa (branca) ou sombra completa (preto).

## <a name="prepare-the-scene"></a>Preparar a cena
No **Estúdio Max 3D,** o processo de criação de um material PBR é o seguinte.

Para começar, como pode ver na Cena da Amostra, criámos uma série de objetos de caixa, cada um dos quais representa um tipo diferente de material:

>[!TIP]
>Vale a pena notar antes de começar a criar ativos para a ARR que utiliza **contadores** para medição.  
>Por isso, é aconselhável definir as **unidades do sistema** de cena para **os medidores.** Além disso, é aconselhável quando exportamos para definir unidades para metros nas definições de exportação FBX.

A imagem seguinte ilustra os passos para definir as Unidades do Sistema em metros em 3D Studio Max. No menu principal, aceda à **configuração de**  >  unidades de**configuração de unidades de configuração de unidades**de  >  **configuração** de unidades e na **escala de unidades** do sistema selecione **Medidores**. 
![unidades do sistema](media/3dsmax/system-units.jpg)

Com as unidades do sistema definidas para os metros, podemos começar a criar os nossos modelos. Na nossa cena de amostra, criamos vários objetos de caixa, cada um representando um tipo de material diferente - por exemplo, metal, borracha, plástico etc. 

>[!TIP]
>É uma boa prática criar ativos para nomear o nome adequado à medida que vai. Isto vai torná-los mais fáceis de encontrar mais tarde se a cena tem um monte de objetos

![objetos de renome](media/3dsmax/rename-objects.jpg)

## <a name="assign-materials"></a>Atribuir materiais

Com alguns objetos criados na nossa cena - neste caso, uma série de cubos, podemos começar a configuração do PBR:

* Na barra de ferramentas principal, clique no ícone **Do Editor de Material** como mostrado na imagem seguinte. Também pode tocar **em M** no seu teclado para abrir o editor. O editor de materiais tem dois modos que podem ser selecionados no dropdown **de Modos** - o modo **de editor de material Compacto** e o modo material de **ardósia.** Como esta cena é relativamente simples, usaremos o **modo compacto.**

* Dentro do editor de materiais, você verá uma série de esferas - estas esferas são os nossos materiais. Vamos atribuir um destes materiais a cada objecto- caixa - na nossa cena. Para fazer esta atribuição, selecione primeiro um dos objetos no viewport principal. Com esta seleção feita, clique na primeira esfera na janela do editor de material. Uma vez atribuído a um objeto, o seu material selecionado será destacado como mostrado na imagem seguinte.

* Clique no botão **Atribuir Material à Seleção** como mostrado. O material selecionado foi agora atribuído ao objeto selecionado.
![atribuir material](media/3dsmax/assign-material.jpg)

No Editor de Materiais, pode selecionar tipos de materiais a partir de uma ampla seleção, dependendo da sua caixa de utilização. Normalmente, o tipo de material é definido como **Standard** por padrão. Este material é um material básico que não é adequado para a configuração de PBR, por isso vamos precisar de alterar o tipo de material para um material PBR. O material **3DSMax** preferido para os projetos de renderização remota Azure é o **Material Físico.**

* No editor de material, clique no separador **Standard** e no navegador material/mapa que abre o **material/mapa**que abre o material físico. Esta ação converterá o material **Standard** atribuído a um **Material Físico**PBR.
![material físico](media/3dsmax/physical-material.jpg)

* No editor de materiais, você vai agora ver as propriedades para o material físico (ver abaixo) e podemos começar a atribuir texturas ao ativo.
![texturas lista](media/3dsmax/textures-list.jpg)

Como se pode ver a partir da imagem acima, há uma ampla gama de mapas e texturas que podem ser adicionados ao material. Para os nossos propósitos, no entanto, usaremos apenas cinco ranhuras de textura no material.

>[!TIP]
>É uma boa prática nomear os seus materiais de forma adequada, como mostra a imagem acima.

Podemos agora começar a considerar atribuir texturas ao nosso material. A forma como gera as suas texturas pode variar de acordo com a preferência ou mesmo de acordo com o uso. Por exemplo, você pode ficar feliz em usar texturas de inclinação que podem ser aplicadas a qualquer ativo ou você pode exigir partes específicas de um projeto/ativo para ter seu próprio conjunto de texturas personalizadas. Você pode querer usar texturas genéricas de inclinação obtidas on-line ou criá-las você mesmo em apps como **Photoshop,** **Quixel Suite,** **Substance Suite** etc. 

Antes de começarmos a atribuir as nossas texturas, mas teremos de considerar as coordenadas de textura dos nossos ativos (UVW). Embora seja melhor a aplicação de texturas a um modelo para garantir que o modelo foi desembrulhado (as texturas não serão apresentadas corretamente sem o desembrulhamento UV adequado), é importante para os nossos propósitos se pretendemos usar um mapa **de Oclusão Ambiente** no nosso modelo. Ao contrário do **Stingray Shader** em **Maya,** o **Material Físico** em **3DSMax** não tem uma ranhura dedicada à textura **de oclusão ambiente.** Por isso, aplicaremos o mapa AO a outra ranhura e permitiremos que seja usado separadamente das outras texturas (texturas de inclinação, por exemplo), atribuímos-lhe um canal de mapa UVW próprio. 

Começaremos por atribuir um **modificador UVW desembrulhador** ao nosso modelo, como mostra abaixo:

* No editor de propriedades de objetos selecionados, clique na Lista de Modificadores e no drop-down que abre para baixo e selecione Desembrulhar UVW. Esta ação aplicará um modificador UVW desembrulhador ao nosso ativo.
![desembrulhar modificador](media/3dsmax/unwrap-modifier.jpg)

* O canal do mapa está definido para um. É no canal de mapa um que o seu principal desembrulho será normalmente feito. No nosso caso, o objeto foi desembrulhado sem coordenadas de textura sobrepostas (UV).
![desembrulhado-uvw](media/3dsmax/unwrapped-uvw.jpg)

O próximo passo é criar um segundo canal de mapa UV.

* Feche o editor UV se estiver aberto e na secção de canais do menu **Edit UV** altere o número do canal para dois. O canal 2 do mapa é o canal esperado para mapas de oclusão ambiente. 

* No diálogo **de aviso de mudança** de canal que se abre, será-lhe dada a opção de **mover** os UV's existentes no canal 1 para o novo canal 2 ou **abandonar** os UV's existentes que criarão automaticamente um novo **UV Desembrulhar.** Selecione **Abandon** se pretender criar um novo **desembrulhamento UV** para o mapa de oclusão ambiente que difere dos UV no canal de mapa 1 (por exemplo, se quiser usar texturas de inclinação no canal 1). Para os nossos propósitos, **vamos mover** os UV's do canal um para o canal 2, uma vez que não precisamos de editar o novo canal UV.

>[!NOTE]
>Mesmo que tenha copiado - **Movido** - o UV desembrulhar do canal de mapa 1 para o canal de mapa 2, pode fazer quaisquer edições necessárias para o novo canal UV's sem afetar o canal de mapa original.

![mudança de canal](media/3dsmax/channel-change.jpg)

Com o novo canal de mapas criado podemos voltar ao material físico no editor de material e começar a adicionar as nossas texturas a ele. Em primeiro lugar, adicionaremos o mapa de oclusão ambiente **(AO),** uma vez que há mais um passo a dar para permitir que funcione corretamente. Uma vez que o mapa AO esteja ligado ao nosso material, precisamos instruí-lo a usar o canal de mapa 2.

* Como mencionado anteriormente, não existe uma ranhura dedicada para mapas AO no **Material Físico 3DSMax**. Em vez disso, aplicaremos o mapa AO à ranhura **difusa de Aspersão.**

* Na lista de **Mapas Genéricos** do material físico, clique na ranhura **"No Map"** da **Diffuse Roughness**e carregue o mapa AO.

* Nas propriedades de texturas AO, você verá o canal de mapa definido para **1** por padrão. Altere este valor para **2**. Esta ação completa os passos necessários para adicionar o seu mapa de oclusão ambiente.

>[!IMPORTANT]
>Este é um passo importante, especialmente se os seus UV's no canal 2 forem diferentes dos do canal 1, uma vez que o AO não mapeia corretamente com o canal errado selecionado.

![atribuir-ao-mapa](media/3dsmax/assign-ao-map.jpg)

Vamos agora abordar a atribuição do nosso mapa normal ao nosso material PBR. Esta ação difere um pouco de **Maya,** na medida em que o mapa normal não é aplicado diretamente à ranhura do mapa de colisões (não existe uma ranhura normal no **material físico 3DSMax** como tal), mas é adicionado a um modificador de mapas normais, que por si só está ligado à ranhura **normal.**

* Na secção **Mapas Especiais** das nossas propriedades de material físico (no editor de material), clique na ranhura **'No Map's No Map** do **Bump.** 

* No navegador material/mapa, localize e clique no **Normal Bump**. Esta ação irá adicionar um **modificador de bump normal** ao nosso material.

* No **modificador de colisão normal,** clique no **Mapa** **Normal** e localize e, em seguida, carregue o seu mapa normal.

* Verifique se o método está definido para **Tangent** (deve ser por defeito) e, se necessário, para alternar **Flip Green (Y)**.

![mapa normal de ](media/3dsmax/normal-bump.jpg)
 ![ carga de colisão normal](media/3dsmax/load-normal-map.jpg)

Com o nosso mapa normal atribuído corretamente, podemos proceder à atribuição das restantes texturas para completar a nossa configuração de material físico. Este processo é um processo simples, sem configurações especiais a ter em conta. A imagem a seguir mostra o conjunto completo de texturas atribuídas ao nosso material: ![ todas as texturas](media/3dsmax/all-textures.jpg)

Com os seus materiais PBR criados e configurados, vale a pena pensar em instalar objetos na sua cena. A instalação de objetos semelhantes na sua cena - como porcas, parafusos, anilhas de parafusos - essencialmente quaisquer objetos que sejam iguais podem gerar poupanças significativas em termos de tamanho do ficheiro. Os casos de um objeto mestre podem ter a sua própria escala, rotação e transformações para que possam ser colocados como necessário na sua cena. No **Estúdio Max 3D,** o processo de **Instancing** é simples.

* No porto principal, selecione o objeto/objetos que pretende exportar.

* Hold **Shift** e arraste os ativos para cima usando a ferramenta de transformação (mover) 

* No diálogo **Opções Clone** que se abre, fina **o Objeto** para **o Exemplo** e clique **em OK**. 
![caso-objeto](media/3dsmax/instance-object.jpg)

Esta ação criará uma instância do seu objeto que pode ser movido rotativo ou escalado independentemente do seu progenitor e de outros casos desse progenitor.

>[!IMPORTANT]
>No entanto - quaisquer alterações que faça a uma instância enquanto estiver no modo sub-objeto será transmitida a todas as instâncias do seu objeto, por isso, se estiver a trabalhar com componentes de objetos de instância - vértices, rostos de polígono, etc. certifique-se primeiro de que pretende que quaisquer alterações que faça para afetar todos estes casos. Lembre-se que qualquer objeto de instância pode ser transformado num objeto único a qualquer momento. 

>[!TIP]
>As melhores práticas em relação à instancing na sua cena é criá-las à medida que avança, já que substituir **Cópias** por Objetos Instanced mais tarde é extremamente difícil. 

Uma última coisa a ter em conta antes de passarmos ao processo de exportação é como você pode querer embalar a sua cena/ativo para a partilha. Idealmente, se passar o ativo para um cliente ou membro da equipa, vai querer que eles sejam capazes de abrir e ver o ativo como deve ser visto com um mínimo de alarido. É importante, portanto, manter os seus caminhos de textura de ativos em relação ao arquivo de cena. Se os caminhos de textura do seu ativo estiverem a apontar para uma unidade local ou um caminho/local absoluto, eles não carregarão para a cena se forem abertos num computador diferente, mesmo que o ficheiro **.max** esteja na mesma pasta que as texturas. Tornar os caminhos de textura relativos no Estúdio 3D Max resolve este problema e é bastante simples.

* Na barra de ferramentas **File**principal, aceda ao  >  **Reference**  >  **Toggle de Rastreio de Ativos**de Referência de Ficheiros . 

* No navegador de rastreio de ativos que se abre, verá todas ou a maioria das texturas que aplicou aos seus materiais PBR listados na coluna **Maps/Shaders.**

* Ao lado deles na coluna **Caminho Completo,** verá o caminho do ficheiro para a localização das suas texturas, muito provavelmente para a sua localização na sua máquina local.

* Finalmente, verá uma coluna chamada **Status**. Esta coluna indica se uma determinada textura foi localizada e aplicada à sua cena ou não, e irá sinalizar esta textura com um dos seguintes termos **OK**, **Found**, ou **File Missing**. Os dois primeiros indicam que o ficheiro foi encontrado e carregado, enquanto o último, obviamente significa que o localizador não conseguiu localizar um ficheiro.
![textura-caminhos](media/3dsmax/texture-paths.jpg)

Pode notar que nem todas as suas texturas estão listadas no localizador de ativos quando a abrir pela primeira vez. Não há nada com que se preocupar, já que percorrer o processo de descoberta de caminhos uma ou duas vezes geralmente encontra todas as texturas da cena. O processo de descoberta de caminhos é o seguinte: 

* Na janela do **Shift**rastreador de ativos, + **clique** na textura superior na lista **Maps/Shaders** e continue a segurar o Clique de Turno na última textura da lista. Esta ação irá selecionar todas as texturas da lista. As texturas selecionadas serão agora destacadas em azul (ver a imagem acima).

* Clique à direita na seleção e no menu pop-up que se abre, selecione **set Path**.

* Na caixa **Especificar Caminho do Ativo** que se abre, selecione o caminho local para as suas texturas mostradas e substitua-a pelo seguinte `.\` e clique em **OK**. 

* Após um período de tempo (que variará dependendo de quantas texturas existem na sua cena e da sua grande cena) o rastreador de ativos deve resolver-se da seguinte forma (ver imagem).
![resolver texturas](media/3dsmax/resolve-textures.jpg)

Note que a coluna **Caminho Completo** está agora em branco. Significa que a cena já não procura encontrar as texturas relevantes numa localização específica (absoluta), mas irá sempre encontrá-las enquanto o ficheiro max ou o ficheiro FBX relacionado estiverem na mesma pasta que as texturas. 

>[!NOTE]
>Pode ocorrer às vezes que você tem que repetir este processo algumas vezes para encontrar e resolver todas as texturas e caminhos. Isto não é nada com que se preocupar, apenas repita até que todos os ativos relevantes sejam contabilizados. Pode também ser o caso de alguns ficheiros já não serem encontrados. Neste caso, basta selecionar todos os ativos da lista e clicar em **Remover Caminhos Desaparecidos** (ver imagem acima)

## <a name="fbx-export"></a>Exportação de FBX

Com o rastreio de ativos completo, podemos agora passar para a exportação FBX. Mais uma vez, o processo é simples e pode ser feito de várias maneiras. 

>[!TIP]
>É uma boa prática que, a menos que deseje exportar toda a sua cena, selecione para exportação apenas os ativos que são necessários. Em cenas particularmente intensivas de recursos, a exportação pode demorar muito tempo, por isso faz sentido exportar apenas o que você precisa
>
>É aconselhável que, se tiver usado modificadores como **o Turbosmooth** ou **o Open SubDiv,** etc. que os destrua antes de exportar, pois podem causar problemas durante a exportação. Salve sempre a sua cena antes de fazer isto! 

* No local, selecione os ativos que pretende exportar e na barra de ferramentas principal, vá para **File**  >  **Export**  >  **Export Selected**

* No **ficheiro select para exportar** o diálogo, escreva ou selecione o nome do ficheiro de saída e nas opções **Save as Type** selecione **Autodesk (*.fbx)**. Esta ação abrirá o menu de exportação FBX. 

* Lembre-se, se criou casos na sua cena que é importante que **o Preserve Instances** seja ligado nas definições de exportação de FBX. 
![fbx-exportação](media/3dsmax/fbx-export.jpg)

Lembre-se que anteriormente foi mencionado que havia algumas maneiras de exportar o nosso arquivo. Se a intenção ao exportar for a de que o FBX seja partilhado juntamente com os seus ficheiros de texturas numa pasta/diretório, então as definições mostradas na imagem abaixo devem aplicar-se e funcionar bem. Uma vez selecionadas as suas definições, clique **em OK**.
![fbx-configurações](media/3dsmax/fbx-settings.jpg)

No entanto, se preferir não partilhar grandes pastas/diretórios de texturas juntamente com o FBX, pode optar por **incorporar** as texturas no FBX. Significa que todo o ativo - texturas incluídas - será adicionado a um único FBX. Esteja ciente, no entanto, enquanto combina a sua exportação num único ativo, o ficheiro FBX será consideravelmente maior como resultado.

>[!IMPORTANT]
>Se o seu resultado ficheiro FBX for maior que 2.4GB, então a versão mínima das definições de exportação de FBX (ver acima) deve ser 2016 ou mais recente. Como as versões mais recentes têm suporte de 64 bits e, assim, suportam ficheiros maiores.

* Nas definições de exportação FBX, alternar em **Embed Media e, em seguida, clique em **OK** para exportar com texturas incluídas. 

Ao exportar para fBX durante a utilização do Material Físico, provavelmente verá o seguinte pop-up de aviso depois de clicar em 'OK' no diálogo de exportação: ![ avisos de exportação](media/3dsmax/export-warnings.jpg)

Este aviso informa simplesmente o utilizador de que os materiais exportados podem não ser compatíveis com outros pacotes de software. Como o material físico é compatível com a renderização remota Azure, não é nada com que se preocupar. Basta clicar **EM OK** para completar o processo e fechar a janela.

## <a name="conclusion"></a>Conclusão

Em geral, este tipo de material parece mais realista porque é baseado na física da luz do mundo real. Cria um efeito imersivo adicional para que a cena pareça existir no mundo real.

## <a name="next-steps"></a>Passos seguintes

Agora sabe como configurar materiais com iluminação avançada para objetos numa cena. Também sabe exportar os objetos para o formato FBX que é suportado pela Azure Remote Rendering. O próximo passo é converter o ficheiro FBX e visualizá-lo em Renderização Remota Azure.

>[!div class="nextstepaction"]
>[Quickstart: Converter um modelo para renderização](../../quickstarts\convert-model.md)