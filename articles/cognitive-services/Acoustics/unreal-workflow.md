---
title: Tutorial de Design Irreal do Projeto Acoustics
titlesuffix: Azure Cognitive Services
description: Este tutorial descreve o fluxo de trabalho de design para o Projeto Acústica em Unreal e Wwise.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: 817a11171c5b4b4ef205e5fbb04f9b6d6d85b248
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "68854250"
---
# <a name="project-acoustics-unrealwwise-design-tutorial"></a>Tutorial de Design Acústico Irreal/Wwise
Este tutorial descreve a configuração do design e fluxo de trabalho para o Projeto Acústica em Unreal e Wwise.

Pré-requisitos de software:
* Um projeto irreal com o Projeto Acoustics Wwise e plugins irreais

Para obter um projeto irreal com o Projeto Acústica, você pode:
* Siga as instruções de [integração irreal](unreal-integration.md) do Projeto Acústica para adicionar O Projeto Acústica ao seu projeto Irreal
* Ou, use o projeto de amostra do [Projeto Acústica.](unreal-quickstart.md)

## <a name="setup-project-wide-wwise-properties"></a>Configurar propriedades Wwise em todo o projeto
Wwise tem curvas globais de obstrução e oclusão que afetam a forma como o plugin Project Acoustics conduz o DSP áudio Wwise.

### <a name="design-wwise-occlusion-curves"></a>Curvas de oclusão Wwise de design
Quando o Project Acoustics está ativo, responde ao volume de oclusão, filtro de baixo passe (LPF) e curvas de filtro de alta passagem (HPF) que definiu em Wwise. Recomendamos que o seu tipo de curva de volume seja linear com um valor de -100 dB para um valor de oclusão de 100.

Com esta definição, se a simulação do Projeto Acústica calcular uma oclusão de -18 dB, irá inserir para a curva abaixo em X=18, e o valor Y correspondente é a atenuação aplicada. Para fazer metade da oclusão, derpõe o ponto final para -50 dB em vez de -100 dB, ou para -200 dB para exagerar a oclusão. Podes adaptar e afinar qualquer curva que funcione melhor para o teu jogo.
 
![Screenshot do editor da curva de oclusão de Wwise](media/wwise-occlusion-curve.png)

### <a name="disable-wwise-obstruction-curves"></a>Desativar as curvas de obstrução wwise
As curvas de obstrução Wwise afetam o nível seco isoladamente, mas o Project Acoustics utiliza controlos de design e simulação para impor rácios molhados/secos. Recomendamos que desabote a curva do volume de obstrução. Para conceber a molhada, utilize o controlo Wetness Adjust descrito posteriormente.
 
Se estiver a utilizar curvas LPF/HPF de obstrução para outros fins, certifique-se de que as definiu para Y=0 em X=0 (isto é, não existe LPF ou HPF quando não há obstrução).

![Screenshot do editor de curva de obstrução wwise](media/wwise-obstruction-curve.png)

### <a name="design-project-acoustics-mixer-parameters"></a>Parâmetros de misturador de mixer de projeto de design
Você pode controlar propriedades de reverberação global visitando o separador plugin mixer do Project Acoustics Bus. Clique duas vezes em "Project Acoustics Mixer (Custom)" para abrir o painel de definições do plugin mixer.

Também pode ver que o plugin misturador tem uma opção "Executar a Espacialização". Se preferir utilizar a espacialização incorporada do Project Acoustic, verifique a caixa de verificação "Execute a Spatialization" e escolha entre hrTF ou Panning. Certifique-se de desativar quaisquer autocarros Dry Aux que tenha montado, caso contrário ouvirá o caminho direto duas vezes. Utilize o "Wetness Adjust" e o "Reverb Time Scale Fator" para exercer o controlo global da mistura reverb. Note que tem de reiniciar O Unreal e depois regenerar os bancos de som antes de tocar para apanhar alterações de config de plugin misturadora, como a caixa de verificação 'Perform Spatialization'.

![Screenshot das opções de plugin mixer Project Acoustics Wwise](media/mixer-plugin-global-settings.png)

## <a name="set-project-acoustics-design-controls-in-the-wwise-actor-mixer-hierarchy"></a>Definir os controlos de design de acústica do projeto na hierarquia wwise actor-mixer
Para controlar os parâmetros de um actor-mixer individual, clique duas vezes no Actor-Mixer e, em seguida, clique no seu separador Mixer Plug-in. Aqui poderá alterar quaisquer parâmetros ao nível por som. Estes valores combinam com os definidos do lado irreal (descrito abaixo). Por exemplo, se o plugin Unreal project Acoustics Unreal definir O ajuste de outdooridade num objeto a 0,5, e wwise o definir para -0,25, o ajuste de outdooridade resultante aplicado a esse som é 0.25.

![Screenshot de definições de misturador de som na hierarquia wwise actor-mixer](media/per-sound-mixer-settings.png)

### <a name="ensure-the-aux-bus-has-dry-send-and-output-bus-has-wet-send"></a>Certifique-se de que o autocarro aux tem envio seco, e o ônibus de saída tem envio molhado
Lembre-se que a configuração de actor-mixer necessária troca o habitual encaminhamento seco e húmido em Wwise. Produz sinal reverbo no autocarro de saída do actor-misturador (definido para Project Acoustics Bus) e sinal seco ao longo do autocarro aux definido pelo utilizador. Este encaminhamento é necessário devido às características da API da misturadora Wwise que o plugin Project Acoustics Wwise utiliza.

![Screenshot do editor da Wwise mostrando diretrizes de design de voz para Project Acoustics](media/voice-design-guidelines.png)
 
### <a name="set-up-distance-attenuation-curves"></a>Configurar curvas de atenuação de distância
Certifique-se de que qualquer curva de atenuação utilizada por actor-mixers que utilizem o Project Acoustics tenha o conjunto de envio seleto definido pelo utilizador para "volume de autocarro de saída". Wwise fá-lo por padrão para curvas de atenuação recém-criadas. Se está a migrar um projeto existente, verifique as definições da curva.

Por padrão, a simulação do Projeto Acústica tem um raio de 45 metros em torno da localização do jogador. Geralmente recomendamos que a sua curva de atenuação seja de -200 dB em torno dessa distância. Esta distância não é uma restrição difícil. Para alguns soa como armas, pode querer um raio maior. Nesses casos, a ressalva é que apenas a geometria dentro de 45 m da localização do jogador irá participar. Se o leitor estiver numa sala e uma fonte de som estiver fora da sala e a 100 m de distância, será devidamente obstruído. Se a fonte estiver numa sala e o jogador estiver lá fora e a 100 metros de distância, não será devidamente obstruído.

![Screenshot das curvas de atenuação de Wwise](media/atten-curve.png)

### <a name="post-mixer-equalization"></a>Igualdade pós-misturadora ###
 Outra coisa que talvez queira fazer é adicionar um equalizador pós-misturador. Pode tratar o autocarro Project Acoustics como um autocarro típico de reverberação (em modo reverbo padrão) e colocar um filtro nele para fazer a igualdade. Pode ver uma amostra deste sinuoso projeto de amostra wwise.

![Screenshot do QE pós-misturador Wwise](media/wwise-post-mixer-eq.png)

Por exemplo, um filtro de passe elevado pode ajudar a lidar com o baixo a partir de gravações próximas do campo que produzem reverberação boomy e irrealista. Também pode obter mais controlo pós-cozedura ajustando o QE através de RTPCs, permitindo-lhe alterar a cor do reverbo no momento do jogo.

## <a name="set-up-scene-wide-project-acoustics-properties"></a>Configurar propriedades acústicas do projeto em toda a cena

O ator do Espaço Acústico expõe muitos controlos que modificam o comportamento do sistema e são úteis na depuração.

![Screenshot dos controlos espaciais da acústica irreal](media/acoustics-space-controls.png)

* **Dados da Acústica:** Este campo deve ser atribuído a um ativo de acústica assado do diretório Conteúdo/Acústica. O plugin Project Acoustics adicionará automaticamente o diretório Conteúdo/Acústica aos diretórios embalados do seu projeto.
* **Tamanho do azulejo:** As extensões da região em torno do ouvinte que você quer dados de acústica carregados em RAM. Enquanto as sondas ouvintes forem carregadas imediatamente em torno do leitor, os resultados são os mesmos que carregar dados acústicos para todas as sondas. Azulejos maiores usam mais RAM, mas reduzem o disco I/O
* **Fluxo Automático:** Quando ativado, carrega automaticamente em azulejos novos à medida que o ouvinte atinge a borda de uma região carregada. Quando desativado, terá de carregar novos azulejos manualmente através de código ou plantas
* **Escala cache:** controla o tamanho da cache utilizada para consultas acústicas. Uma cache menor usa menos RAM, mas pode aumentar o uso de CPU para cada consulta.
* **Acústica Ativada:** Um controlo de depuração para permitir um rápido desvio de A/B da simulação acústica. Este controlo é ignorado nas configurações de envio. O controlo é útil para descobrir se um determinado bug áudio origina-se nos cálculos da acústica ou em qualquer outro problema no projeto Wwise.
* **Distâncias de atualização:** Utilize esta opção se quiser utilizar as informações de acústica pré-assadas para consultas à distância. Estas consultas são semelhantes aos moldes de raios, mas foram pré-calculadas, por isso, tomem muito menos CPU. Um exemplo de utilização é para reflexos discretos fora da superfície mais próxima do ouvinte. Para alavancar totalmente isto, terá de usar código ou plantas para consultar distâncias.
* **Desenhar estatísticas:** Embora `stat Acoustics` a UE possa fornecer-lhe informações sobre cpu, este ecrã de estado mostrará o ficheiro ACE atualmente carregado, uso de RAM e outras informações de estado na parte superior esquerda do ecrã.
* **Desenhe Voxels:** Sobreponha voxels perto do ouvinte mostrando a grelha voxel usada durante a interpolação do tempo de funcionação. Se um emissor estiver dentro de um voxel de tempo de funcionação, falhará as consultas acústicas.
* **Desenhar sondas:** Mostre todas as sondas para esta cena. Serão cores diferentes dependendo do seu estado de carga.
* **Distâncias de sorteio:** Se as Distâncias de Atualização estiverem ativadas, isto mostrará uma caixa na superfície mais próxima do ouvinte em direções quantificadas em torno do ouvinte.

## <a name="actor-specific-acoustics-design-controls"></a>Controlos de design de acústica específicos para atores
Estes controlos de design são remetos a um componente de áudio individual em Unreal.

![Screenshot dos controlos de componentes de áudio irreais](media/audio-component-controls.png)

* **Multiplicador de oclusão:** Controla o efeito de oclusão. Os valores > 1 amplificarão a oclusão. Os valores <1 irão minimizá-lo.
* **Ajuste da molhada:** Reverberação adicional dB
* **Multiplicador** de tempo de decadência: Controla o RT60 multiplicativamente, com base na saída da simulação acústica
* **Regulação da outdooridade:** Controla como é a reverberação ao ar livre. Os valores mais próximos de 0 são mais interiores, mais perto de 1 são mais ao ar livre. Este ajuste é aditivo, por isso, defini-lo para -1 irá impor dentro de casa, definindo-o como +1 irá impor ao ar livre.
* **Transmissão Db:** Render um som adicional através da parede com este ruído combinado com atenuação da distância baseada na linha de visão.
* **Dobra da distância da relação húmida:** Ajusta as características de reverberação na fonte como se estivesse mais perto/mais longe, sem afetar o caminho direto.
* **Jogar no Início:** Alternar para especificar se o som deve reproduzir-se automaticamente no início da cena. Ativado por predefinição.
* **Mostrar parâmetros acústicos:** Exiba informações sobre bug diretamente em cima do componente no jogo. (apenas para configurações não-expediis)

## <a name="blueprint-functionality"></a>Funcionalidade de planta
O ator do Espaço Acústico está acessível através de uma planta, fornecendo funcionalidades como carregar um mapa ou modificar configurações através de scripts de nível. Damos dois exemplos aqui.

### <a name="add-finer-grained-control-over-streaming-load"></a>Adicione um controlo mais fino sobre a carga de streaming
Para gerir os dados acústicos que se transmitem em vez de transmitir automaticamente com base na posição do jogador, pode utilizar a função de planta de aço de carga de força:

![Screenshot das opções de streaming de plantas em Unreal](media/blueprint-streaming.png)

* **Alvo:** O ator da AcousticsSpace
* **Posição central:** O centro da região que precisa de dados carregados
* **Descarregar sondas fora de azulejos:** Se forem verificados, todas as sondas que não estejam na nova região serão descarregadas da RAM. Se não for controlada, a nova região é carregada na memória, deixando as sondas existentes também carregadas na memória
* **Bloco em Conclusão:** Faz com que o azulejo carregue uma operação sincronizada

O tamanho do azulejo já deve ser definido antes de chamar o Azulejo de Carga Forçada. Por exemplo, você poderia fazer algo assim para carregar um ficheiro ACE, definir o seu tamanho de azulejo, e fluir em uma região:

![Screenshot das opções de configuração de streaming em Unreal](media/streaming-setup.png)

A função de planta de dados de acústica de carga utilizada neste exemplo tem os seguintes parâmetros:

* **Alvo:** O ator da AcousticsSpace.
* **Novo Bolo:** O ativo de dados da acústica a ser carregado. Deixando este vazio/defini-lo nulo descarregará a cozedura atual sem carregar uma nova.

### <a name="optionally-query-for-surface-proximity"></a>Consulta opcional para proximidade de superfície
Se quiser ver quão próximas as superfícies estão numa determinada direção em torno do ouvinte, pode utilizar a função Query Distance. Esta função pode ser útil para impulsionar reflexos atrasados direcionais, ou para outra lógica do jogo impulsionada pela proximidade da superfície. A consulta é mais barata do que um ray-cast porque os resultados são retirados da mesa de procura acústica.

![Screenshot de exemplo Consulta de distância blueprint](media/distance-query.png)

* **Alvo:** O ator da AcousticsSpace
* **Direção do olhar:** A direção para consultar, centrado no ouvinte
* **Distância:** Se a consulta for bem sucedida, a distância até à superfície mais próxima
* **Valor de Devolução:** Boolean - verdadeiro se a consulta foi bem sucedida, caso contrário falsa

## <a name="next-steps"></a>Passos seguintes
* Explore os conceitos por trás do processo de [design](design-process.md)
* [Crie uma conta Azure](create-azure-account.md) para assar a sua própria cena


