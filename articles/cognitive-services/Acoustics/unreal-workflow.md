---
title: Tutorial de design não real de projetos acústicos
titlesuffix: Azure Cognitive Services
description: Este tutorial descreve o fluxo de trabalho de design para acústicas do projeto em inreal e WWise.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: kegodin
ROBOTS: NOINDEX
ms.openlocfilehash: 5061370f43947341bb05bc30fa596604bc27ce74
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68706576"
---
# <a name="project-acoustics-unrealwwise-design-tutorial"></a>Tutorial de design do projeto acústica inreal/WWise
Este tutorial descreve a configuração de design e o fluxo de trabalho para acústicas do projeto em inreal e WWise.

Pré-requisitos de software:
* Um projeto inreal com o projeto acústica WWise e plugins inreais

Para obter um projeto inreal com acústicas do projeto, você pode:
* Siga o [projeto acústica](unreal-integration.md) as instruções de integração inreal para adicionar acústicas de projeto ao seu projeto inreal
* Ou use o [projeto de exemplo acústica do projeto](unreal-quickstart.md).

## <a name="setup-project-wide-wwise-properties"></a>Configurar propriedades de WWise de todo o projeto
WWise tem uma obstrução global e curvas oclusão que afetam o modo como o plug-in acústico do projeto orienta o DSP de áudio WWise.

### <a name="design-wwise-occlusion-curves"></a>Design WWise curvas oclusão
Quando o projeto acústica está ativo, ele responde às curvas de volume oclusão, filtro baixo de passagem (LPF) e filtro de passagem alta (HPF) que você define em WWise. É recomendável definir o tipo de curva de volume como linear com um valor de-100 dB para um valor de oclusão de 100.

Com essa configuração, se a simulação de acústica do projeto computa um oclusão de-18 dB, ela será inserida na curva abaixo em X = 18, e o valor Y correspondente será a atenuação aplicada. Para fazer metade de oclusão, defina o ponto de extremidade para-50 dB em vez de-100 dB ou para-200 dB para exagerar oclusão. Você pode personalizar e ajustar qualquer curva que funcione melhor para seu jogo.
 
![Captura de tela do editor de curva WWise oclusão](media/wwise-occlusion-curve.png)

### <a name="disable-wwise-obstruction-curves"></a>Desabilitar curvas de obstrução de WWise
As curvas de obstrução de WWise afetam o nível seco no isolamento, mas os acústicos do projeto usam os controles de design e a simulação para impor proporções úmida/secantes. Recomendamos desabilitar a curva de volume de obstrução. Para criar o umidade, use o controle de ajuste umidade descrito posteriormente.
 
Se você estiver usando as curvas LPF/HPF de obstrução para outras finalidades, certifique-se de defini-las como Y = 0 em X = 0 (ou seja, não há LPF ou HPF quando não houver obstrução).

![Captura de tela do editor de curva de obstrução de WWise](media/wwise-obstruction-curve.png)

### <a name="design-project-acoustics-mixer-parameters"></a>Criar parâmetros do mixer do projeto acústicos
Você pode controlar as propriedades de reverberação globais visitando a guia plug-in do mixer do barramento acústico do projeto. Clique duas vezes em "mixer acústicos do projeto (personalizado)" para abrir o painel de configurações do plug-in do mixer.

Você também pode ver que o plug-in do mixer tem uma opção "executar a espacial". Se você preferir usar a espacial interna do projeto acústico, marque a caixa de seleção "executar a espacial" e escolha entre HRTF ou panorâmica. Certifique-se de desabilitar os barramentos auxiliares secas que você configurou, caso contrário, você ouvirá o caminho direto duas vezes. Use o "umidade ajuste" e o "fator de escala de tempo de reverberação" para exercitar o controle global na combinação de reverberação. Observação Você deve reiniciar inreal e, em seguida, regenerar soundbanks antes de pressionar reproduzir para obter as alterações de configuração do plug-in do mixer, como a caixa de seleção "executar a espacial".

![Captura de tela de opções do plugin do mixer WWise do projeto acústicos](media/mixer-plugin-global-settings.png)

## <a name="set-project-acoustics-design-controls-in-the-wwise-actor-mixer-hierarchy"></a>Definir controles de design acústicos do projeto na hierarquia de WWise ator-mixer
Para controlar os parâmetros de um mixer de ator individual, clique duas vezes no mixer de ator e, em seguida, clique na guia de plug-in do mixer. Aqui você poderá alterar todos os parâmetros no nível por som. Esses valores se combinam com aqueles definidos do lado não real (descrito abaixo). Por exemplo, se o projeto acústica o plugin não real define o ajuste de importabilidade em um objeto como 0,5 e WWise o define como-0,25, o ajuste de importabilidade resultante aplicado a esse som é 0,25.

![Captura de tela das configurações do mixer de som no WWise ator – hierarquia do mixer](media/per-sound-mixer-settings.png)

### <a name="ensure-the-aux-bus-has-dry-send-and-output-bus-has-wet-send"></a>Verifique se o barramento auxiliar tem envio seco e se o barramento de saída tem envio molhado
Lembre-se de que a configuração de ator-mixer necessária troca o roteamento comum seco e úmida em WWise. Ele produz um sinal de reverberação no barramento de saída de ator-mixer (definido para o barramento acústico do projeto) e um sinal seco ao longo do barramento auxiliar definido pelo usuário. Esse roteamento é necessário devido aos recursos da API de plug-in do mixer do WWise que o projeto acústica o plug-in WWise usa.

![Captura de tela do editor WWise mostrando as diretrizes de design de voz para acústicas do projeto](media/voice-design-guidelines.png)
 
### <a name="set-up-distance-attenuation-curves"></a>Configurar curvas de atenuação de distância
Verifique se qualquer curva de atenuação usada por ator-mixers usando acústicas de projeto tem o envio de aux definido pelo usuário definido como "volume de barramento de saída". O WWise faz isso por padrão para curvas de atenuação recém-criadas. Se você estiver migrando um projeto existente, verifique as configurações de curva.

Por padrão, a simulação acústica do projeto tem um raio de 45 metros em volta do local do jogador. Geralmente, é recomendável definir sua curva de atenuação para-200 dB em volta dessa distância. Essa distância não é uma restrição rígida. Para alguns sons como armas, talvez você queira um raio maior. Nesses casos, a advertência é que apenas a geometria dentro de 45 m do local do jogador participará. Se o jogador estiver em uma sala e uma fonte de som estiver fora da sala e for 100 ms de distância, ela será obstruído corretamente. Se a fonte estiver em uma sala e o jogador estiver fora e 100 m de distância, ele não será obstruído corretamente.

![Captura de tela das curvas de atenuação de WWise](media/atten-curve.png)

### <a name="post-mixer-equalization"></a>Equalização do mixer de postagem ###
 Uma outra coisa que você pode querer fazer é adicionar um equalizador post. Você pode tratar o barramento acústico do projeto como um barramento de reverberação típico (no modo de reverberação padrão) e colocar um filtro nele para fazer a equalização. Você pode ver um exemplo disso no projeto de exemplo WWise acústicas do projeto.

![Captura de tela do WWise pós-mixer EQ](media/wwise-post-mixer-eq.png)

Por exemplo, um filtro de alta passagem pode ajudar a lidar com o baixo de gravações de campo que geram um reverberação incorrido e não real. Você também pode obter mais controle após o sobreposição ajustando o EQ por meio de RTPCs, permitindo que você altere a cor do verbo de reverberação em tempo de jogo.

## <a name="set-up-scene-wide-project-acoustics-properties"></a>Configurar propriedades acústicas do projeto em toda a cena

O ator do espaço acústicos expõe muitos controles que modificam o comportamento do sistema e são úteis na depuração.

![Captura de tela de controles de espaço acústicos inreais](media/acoustics-space-controls.png)

* **Dados acústicos:** Esse campo deve ser atribuído a um ativo de inclusas acústicos do diretório de conteúdo/acústicos. O plug-in acústica do projeto adicionará automaticamente o conteúdo/diretório acústicos aos diretórios empacotados do projeto.
* **Tamanho do bloco:** As extensões da região em volta do ouvinte que você deseja que os dados acústicos sejam carregados na RAM. Desde que as investigações de ouvinte imediatamente em todo o jogador sejam carregadas no, os resultados são os mesmos que o carregamento de dados acústicos para todas as investigações. Blocos maiores usam mais RAM, mas reduzem e/s de disco
* **Fluxo automático:** Quando habilitado, o é carregado automaticamente em novos blocos enquanto o ouvinte atinge a borda de uma região carregada. Quando desabilitado, você precisará carregar novos blocos manualmente por meio de código ou plantas
* **Escala de cache:** controla o tamanho do cache usado para consultas acústicas. Um cache menor usa menos RAM, mas pode aumentar o uso da CPU para cada consulta.
* **Acústicos habilitados:** Um controle de depuração para habilitar a alternância rápida/B da simulação acústica. Esse controle é ignorado nas configurações de envio. O controle é útil para localizar se um determinado bug de áudio se origina nos cálculos acústicos ou algum outro problema no projeto WWise.
* **Atualizar distâncias:** Use esta opção se você quiser usar as informações acústicas inclusas para consultas de distância. Essas consultas são semelhantes a Ray casts, mas foram previamente computadas, portanto, levam muito menos CPU. Um exemplo de uso é para reflexões discretas da superfície mais próxima ao ouvinte. Para aproveitar ao máximo isso, você precisará usar código ou plantas para consultar distâncias.
* **Estatísticas de desenho:** Embora a UE `stat Acoustics` possa fornecer informações de CPU, essa exibição de status mostrará o arquivo Ace carregado atualmente, o uso de RAM e outras informações de status no canto superior esquerdo da tela.
* **Desenhar voxels:** Sobreposição voxels perto do ouvinte mostrando a grade VOXEL usada durante a interpolação do tempo de execução. Se um emissor estiver dentro de um VOXEL de tempo de execução, ele falhará nas consultas acústicas.
* **Desenhar investigações:** Mostrar todas as investigações para esta cena. Elas serão cores diferentes, dependendo do estado de carregamento.
* **Distâncias de desenho:** Se as distâncias de atualização estiverem habilitadas, isso mostrará uma caixa na superfície mais próxima ao ouvinte em direções quantificadas ao ouvinte.

## <a name="actor-specific-acoustics-design-controls"></a>Controles de design acústicos específicos de ator
Esses controles de design são incluídos no escopo de um componente de áudio individual em um não real.

![Captura de tela de controles de componente de áudio não reais](media/audio-component-controls.png)

* **Multiplicador de oclusão:** Controla o efeito de oclusão. Os valores > 1 aumentarão o oclusão. Os valores < 1 o minimizarão.
* **Ajuste de umidade:** BD de reverberação adicional
* **Multiplicador de tempo de decaimento:** Controla o RT60 multiplicatively, com base na saída da simulação acústica
* **Ajuste de portabilidade:** Controla como o reverberation está no ar da casa. Os valores mais próximos de 0 são mais inportantes, mais próximos de 1 estão mais no fim de casa. Esse ajuste é aditivo, portanto, configurá-lo como-1 impedirá inportações, definindo-o como + 1 será aplicado em áreas externas.
* **BD de transmissão:** Rendere um som adicional através da parede com essa intensidade combinada à atenuação de distância baseada na linha de visão.
* **Detorção de distância da taxa úmida:** Ajusta as características de reverberation na origem como se estivesse mais perto/distante, sem afetar o caminho direto.
* **Jogue no início:** Alterne para especificar se o som deve ser tocado automaticamente no início da cena. Habilitado por padrão.
* **Mostrar parâmetros acústicos:** Exibir informações de depuração diretamente sobre o componente no jogo. (somente para configurações de não envio)

## <a name="blueprint-functionality"></a>Funcionalidade de Blueprint
O ator do espaço acústicos é acessível por meio do plano gráfico, fornecendo funcionalidades como carregar um mapa ou modificar configurações por meio de scripts de nível. Fornecemos dois exemplos aqui.

### <a name="add-finer-grained-control-over-streaming-load"></a>Adicionar controle mais refinado sobre carga de streaming
Para gerenciar o streaming de dados acústico por conta própria, em vez de transmitir automaticamente com base na posição do jogador, você pode usar a função de plantas de bloco de carga forçada:

![Captura de tela das opções de streaming de plantas em não real](media/blueprint-streaming.png)

* **Alvo** O ator AcousticsSpace
* **Posição do centro:** O centro da região que precisa de dados carregados
* **Descarregar investigações fora do bloco:** Se marcada, todas as investigações que não estão na nova região serão descarregadas da RAM. Se desmarcada, a nova região será carregada na memória, deixando as investigações existentes também carregadas na memória
* **Bloquear ao concluir:** Faz com que o bloco carregue uma operação síncrona

O tamanho do bloco já deve ser definido antes de chamar o bloco de carregamento forçado. Por exemplo, você pode fazer algo assim para carregar um arquivo ACE, definir o tamanho do bloco e transmitir em uma região:

![Captura de tela das opções de configuração de streaming em não real](media/streaming-setup.png)

A função de plano gráfico de dados acústicas de carga usada neste exemplo tem os seguintes parâmetros:

* **Alvo** O ator AcousticsSpace.
* **Novo distorta:** O ativo de dados acústicos a ser carregado. Deixar esse valor em branco/defini-lo como nulo descarregará o dicalado atual sem carregar um novo.

### <a name="optionally-query-for-surface-proximity"></a>Opcionalmente, consulte a proximidade da superfície
Se você quiser ver como as superfícies próximas estão em uma direção específica em relação ao ouvinte, você pode usar a função de distância de consulta. Essa função pode ser útil para a condução de reflexos de atraso direcional ou para outra lógica de jogo controlada pela proximidade da superfície. A consulta é menos cara do que uma Ray-Cast porque os resultados são extraídos da tabela de pesquisa acústica.

![Captura de tela de exemplo de consulta de distância do Blueprint](media/distance-query.png)

* **Alvo** O ator AcousticsSpace
* **Procurar direção:** A direção da consulta, centralizada no ouvinte
* **Alcance** Se a consulta for realizada com sucesso, a distância para a superfície mais próxima
* **Valor de retorno:** Booliano-true se a consulta tiver êxito; caso contrário, false

## <a name="next-steps"></a>Passos Seguintes
* Explore os conceitos por trás do [processo de design](design-process.md)
* [Criar uma conta do Azure](create-azure-account.md) para distortar sua própria cena


