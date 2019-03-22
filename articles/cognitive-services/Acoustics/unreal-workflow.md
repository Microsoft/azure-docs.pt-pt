---
title: Tutorial de Unreal Design Acoustics de projeto
titlesuffix: Azure Cognitive Services
description: Este tutorial descreve o fluxo de trabalho de design do projeto Acoustics no Unreal e Wwise.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: kegodin
ms.openlocfilehash: 57bde67ac2259b3847f59f95eaefba9c6fddf13e
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58316206"
---
# <a name="project-acoustics-unrealwwise-design-tutorial"></a>Tutorial do projeto Acoustics Unreal/Wwise Design
Este tutorial descreve a configuração de design e o fluxo de trabalho do projeto Acoustics no Unreal e Wwise.

Pré-requisitos de software:
* Um projeto de Unreal com os plug-ins de projeto Acoustics Wwise e Unreal

Para obter um projeto de Unreal com Acoustics de projeto, pode:
* Siga os [integração projeto Acoustics Unreal](unreal-integration.md) instruções para adicionar o projeto Acoustics ao seu projeto Unreal
* Em alternativa, utilizar o [projeto de exemplo de projeto Acoustics](unreal-quickstart.md).

## <a name="setup-project-wide-wwise-properties"></a>Todo o projeto Wwise propriedades da instalação
Wwise tem obstrução global e as curvas de oclusão que afetam a forma como o plug-in do projeto Acoustics unidades a DSP áudio Wwise.

### <a name="design-wwise-occlusion-curves"></a>Curvas de oclusão Wwise de design
Quando o projeto Acoustics está ativo responde ao volume oclusão, filtro de passagem de baixa (LPF) e filtro de alta-pass (HPF) curves que define em Wwise. Recomendamos a definição de seu tipo de curva de volume linear com um valor de dB-100 para um valor de oclusão de 100.

Com esta definição, se a simulação de projeto Acoustics computa um oclusão do-18 dB, ele será de entrada para a curva em X = 18 e o Y correspondente valor segue a atenuação aplicada. Para fazer oclusão metade, defina o ponto final para-50 dB em vez de-100 dB ou para-200 dB exaggerate oclusão. Pode adaptar e ajustar qualquer curva que funciona melhor para seu jogo.
 
![Editor de curva de oclusão de captura de ecrã de Wwise](media/wwise-occlusion-curve.png)

### <a name="disable-wwise-obstruction-curves"></a>Desativar as curvas de obstrução Wwise
As curvas de obstrução Wwise afetam o nível de dry isoladamente, mas Acoustics de projeto usa controles de design e a simulação para impor wet/dry proporções. Recomendamos desativar a curva de volume obstrução. Para estruturar a umidade, use o controle de Umidade ajustar de mensagens em fila descrito mais à frente.
 
Se estiver usando curvas LPF/HPF obstrução para outros fins, certifique-se de que tem de defini-los como y=0 em X = 0 (ou seja, não existe LPF ou HPF quando não existe nenhum obstrução).

![Editor de curva de obstrução de captura de ecrã de Wwise](media/wwise-obstruction-curve.png)

### <a name="design-project-acoustics-mixer-parameters"></a>Parâmetros de mixer Acoustics de projeto de design
Pode controlar propriedades de reverberação global, visitando o separador de plug-in do mixer de barramento de Acoustics de projeto. Faça duplo clique em "Projeto Acoustics Mixer (personalizado)" para abrir o painel de definições do mixer do plug-in.

Também pode ver que o plug-in do mixer tem uma opção "Executar Spatialization". Se em vez disso, usaria spatialization incorporada do projeto acústico, marque a caixa de verificação "Spatialization executar" e escolha entre HRTF ou Panning. Certifique-se de desabilitar qualquer barramentos de Dry Aux que configurou, caso contrário, ficará a saber o caminho direto duas vezes. Utilize o "Umidade ajustar" e o "Fator de escala de tempo de reverberação" para exercer controlo global a combinação de reverberação. Tenha em atenção que tem de reiniciar o Unreal e voltar a gerar soundbanks antes de atingir play as alterações à configuração de plug-in de mixer, como a caixa de verificação 'Spatialization executar'.

![Opções de plug-in do mixer de captura de ecrã do projeto Acoustics Wwise](media/mixer-plugin-global-settings.png)

## <a name="set-project-acoustics-design-controls-in-the-wwise-actor-mixer-hierarchy"></a>Definir controlos de design de projeto Acoustics na hierarquia de ator mixer Wwise
Para parâmetros de controle de um ator do mixer individual, faça duplo clique no Mixer de Ator, em seguida, clique no respetivo separador de plug-in do Mixer. Aqui, poderá alterar quaisquer parâmetros ao nível por som. Combinam estes valores por aqueles que o conjunto do lado do Unreal (descrito abaixo). Por exemplo, se o plug-in do projeto Acoustics Unreal define Outdoorness ajuste de um objeto 0,5, e os conjuntos de Wwise-lo para-0.25, o ajuste de Outdoorness resultante aplicada a que som é 0,25.

![Captura de ecrã de acordo com as definições de som mixer na hierarquia de ator mixer Wwise](media/per-sound-mixer-settings.png)

### <a name="ensure-the-aux-bus-has-dry-send-and-output-bus-has-wet-send"></a>Certifique-se de que o barramento de aux tem dry send e barramento de saída tem usando o envio
Lembre-se de que a configuração de ator necessária-mixer intercâmbios o habitual dry e wet roteamento no Wwise. Ele produz o sinal de reverberação no barramento de saída do mixer ator (conjunto de barramento de Acoustics de projeto) e o sinal de dry ao longo do barramento de aux definidas pelo utilizador. Esse roteamento é necessário devido a recursos do Wwise mixer Plug-in API que o plug-in do projeto Acoustics Wwise utiliza.

![Editor de captura de ecrã de Wwise que mostra as diretrizes de design de voz para Acoustics do projeto](media/voice-design-guidelines.png)
 
### <a name="set-up-distance-attenuation-curves"></a>Configurar as curvas de atenuação de distância
Certifique-se de qualquer curva de atenuação utilizada por ator-mixers usando Acoustics de projeto têm aux definidas pelo utilizador enviar definido como "volume de bus de saída". Wwise faz isso por predefinição para as curvas de atenuação recém-criado. Se estiver a migrar um projeto existente, verifique as definições de curva. 

Por predefinição, a simulação de projeto Acoustics tem um raio de medidores 45 em todo o local de player. Normalmente, recomendamos que a definição de sua curva de atenuação para-200 dB em torno essa distância. Essa distância não é uma restrição de disco rígida. Para alguns parece ser armas, pode desejar um raio maior. Nesses casos, a limitação é que apenas a geometria dentro de 45 m da localização do leitor irá participar. Se o player está numa sala e uma origem de som está fora do espaço e 100 milhões de distância, ele irá ser corretamente occluded. Se a origem está numa sala e o player está fora e de 100 milhões de distância, ele não ser corretamente occluded.

![Captura de ecrã de Wwise curvas de atenuação](media/atten-curve.png)

## <a name="set-up-scene-wide-project-acoustics-properties"></a>Configurar propriedades de projeto Acoustics de toda a cena

O ator Acoustics espaço expõe muitos controles que modificam o comportamento do sistema e são úteis para depuração.

![Captura de ecrã dos controles de espaço de Unreal Acoustics](media/acoustics-space-controls.png)

* **Dados de Acoustics:** Este campo tem de atribuir um ativo de acoustics incorporadas do diretório de conteúdo/Acoustics. O plug-in do projeto Acoustics adicionará automaticamente o diretório de conteúdo/Acoustics aos diretórios em pacote do seu projeto.
* **Tamanho de bloco:** As extensões da região em todo o serviço de escuta que pretende que os dados de acoustics carregados na RAM. Enquanto serviço de escuta sondas imediatamente em todo o jogador são carregados no, os resultados são os mesmos como carregar dados acústicos para todas as sondas. Mosaicos de maior utilizam mais RAM, mas reduzem e/s de disco
* **Stream automática:** Quando ativada, carregará automaticamente em novos mosaicos quando o serviço de escuta atinge o limite de uma região de carregá-lo. Quando desativada, terá de carregar novos mosaicos manualmente por meio de código ou esquemas
* **Escala de cache:** controla o tamanho da cache utilizado para consultas acústicos. Uma cache menor usa menos RAM, mas pode aumentar a utilização da CPU para cada consulta.
* **Acoustics ativada:** Um controle de depuração para permitir A rápida / B alternar da simulação Acoustics. Esse controle é ignorado no lançamento de configurações. O controle é útil para encontrar o se um bug de áudio específico têm origem nos cálculos de acoustics ou algum outro problema no projeto Wwise.
* **Atualize distâncias:** Utilize esta opção se gostaria de utilizar as informações de acoustics previamente incorporadas para consultas de distância. Estas consultas são semelhantes às conversões de ray, mas elas foram pré-calculadas reserve muito menos CPU. Um exemplo de utilização destina-se a reflexos discretos desativar a superfície de mais próximo para o serviço de escuta. Para aproveitar totalmente isso, terá de utilizar o código ou esquemas para distâncias de consulta.
* **Estatísticas de desenho:** Embora da UE `stat Acoustics` pode fornecer a com informações de CPU, este ecrã de estado irá mostrar o mapa carregado no momento, a utilização de RAM, e outras informações de estado na parte superior esquerda do ecrã.
* **Desenhe Voxels:** Sobrepor voxels fechar para o serviço de escuta que mostra a grade de voxel utilizada durante a interpolação de tempo de execução. Se for um emissor dentro de um voxel de tempo de execução, ele falhará acústicos consultas.
* **Desenhe sondas:** Mostre todas as sondas para essa cena. Eles serão cores diferentes, dependendo do seu estado de carga.
* **Desenhe distâncias:** Se estiver ativada a distâncias de atualização, mostrará uma caixa na superfície de mais próxima para o serviço de escuta nas direções quantificadas em todo o serviço de escuta.

## <a name="actor-specific-acoustics-design-controls"></a>Controles de design de ator específico acoustics
Esses controles de design estão no âmbito de um componente de áudio individual no Unreal.

![Captura de ecrã do componente de áudio Unreal controles](media/audio-component-controls.png)

* **Multiplicador de oclusão:** Controla o efeito de oclusão. Valores > 1 será aumente oclusão. Valores de < 1 irá minimizá-lo.
* **Ajuste de umidade:** DB de reverberação adicionais
* **Win32/decay multiplicador de tempo:** Controles a RT60 multiplicatively, com base no resultado da simulação acoustics
* **Ajuste de outdoorness:** Controla como pessoas a reverberation é. Aproximar os valores como 0 são mais indoors, mais perto da 1 são mais pessoas. Esta alteração é aditivos, para defini-la como -1 irá impor indoors, defini-la para o + 1 irá impor pessoas.
* **Db de transmissão:** Um som por meio de parede adicional com esta intensidade combinado com linha de visão de composição com a base de atenuação de distância.
* **Distância de rácio wet forma muito:** Ajusta as características de reverberation na origem, como se fosse mais próximo/adicional fora, sem afetar o caminho direto.
* **Mostra parâmetros acústicos:** Apresentar as informações de depuração diretamente sobre o componente no jogo. (apenas para configurações de não envio)

## <a name="blueprint-functionality"></a>Funcionalidade de esquema
O ator Acoustics espaço está acessível através do esquema, fornecendo funcionalidade como carregar um mapa ou modificar as definições através de nível de script. Fornecemos dois exemplos aqui.

### <a name="add-finer-grained-control-over-streaming-load"></a>Adicionar controlo mais refinado sobre a carga de transmissão em fluxo
Para gerir os dados de acústicos de transmissão em fluxo por conta própria, em vez de transmissão em fluxo automaticamente com base na posição do jogador, pode usar a função de esquema de força carga mosaico:

![Opções de captura de ecrã do esquema de transmissão em fluxo de mensagens em fila no Unreal](media/blueprint-streaming.png)

* **Destino:** O ator AcousticsSpace
* **Posição de Center:** O centro da região que tem de dados carregados
* **Descarrega sondas fora de mosaico:** Se a opção estiver marcada, todas as sondas não na nova região será descarregadas da RAM. Se a opção estiver desmarcada, a nova região é carregada na memória enquanto também deixar as sondas existentes carregado na memória
* **Bloquear após a conclusão:** Faz com que o mosaico carregar uma operação síncrona

Tamanho do bloco já tem de ser definido antes de chamar o mosaico da carga de força. Por exemplo, pode fazer algo semelhante a isto para carregar um ficheiro ACE, defina o tamanho do mosaico e transmitir em fluxo numa região:

![Opções de captura de ecrã de configuração de transmissão em fluxo no Unreal](media/streaming-setup.png)

### <a name="optionally-query-for-surface-proximity"></a>Opcionalmente, a consulta de proximidade superfície
Se quiser ver como fechar superfícies estão numa direção específica em todo o serviço de escuta, pode usar a função de distância de consulta. Essa função pode ser útil para impulsionar direcionais reflexos atrasados ou para outra lógica do jogo orientado pelo proximidade superfície. A consulta é mais barato do que um cast ray porque os resultados são extraídos da tabela de referência de acoustics.

![Captura de ecrã da consulta de distância de plano gráfico de exemplo](media/distance-query.png)

* **Destino:** O ator AcousticsSpace
* **Direção do aspeto:** A direção para consultar, centra-se com o serviço de escuta
* **Distância:** Se a consulta for bem-sucedida, a distância para a superfície de mais próxima
* **Valor de retorno:** Booleano – verdadeiro se a consulta foi concluída com êxito, caso contrário FALSO

## <a name="next-steps"></a>Passos Seguintes
* Explore os conceitos por trás do [criar processo](design-process.md)
* [Criar uma conta do Azure](create-azure-account.md) para implantar seu próprios cena


