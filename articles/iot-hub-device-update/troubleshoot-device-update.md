---
title: Resolução de problemas comum de atualização de dispositivo para problemas do Azure IoT Hub | Microsoft Docs
description: Este documento fornece uma lista de dicas e truques para ajudar a resolver muitos problemas possíveis que pode estar a ter com a Atualização do Dispositivo para o IoT Hub.
author: lichris
ms.author: lichris
ms.date: 2/17/2021
ms.topic: troubleshooting
ms.service: iot-hub-device-update
ms.openlocfilehash: 6329e93bb5e628d68afbb2700ce0b9e3a2a711ad
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101680005"
---
# <a name="device-update-for-iot-hub-troubleshooting-guide"></a>Atualização do dispositivo para guia de resolução de problemas do hub IoT

Este documento enumera algumas questões e problemas comuns que os utilizadores da Atualização do Dispositivo reportaram. À medida que a Atualização do Dispositivo progride através da Visualização Pública, este guia de resolução de problemas será atualizado periodicamente com novas questões e soluções. Se encontrar um problema que não apareça neste guia de resolução de problemas, consulte a secção [de Suporte do Microsoft contactando](#contact) para documentar a sua situação.

## <a name="importing-updates"></a><a name="import"></a>Atualizações de importação

### <a name="q-im-having-trouble-connecting-my-device-update-instance-to-my-iot-hub-instance"></a>P: Estou a ter problemas em ligar a minha instância de Atualização de Dispositivos à minha instância IoT Hub.
_Certifique-se de que as suas rotas de mensagem IoT Hub estão configuradas corretamente, de acordo com a documentação de [recursos de Atualização](./device-update-resources.md) de Dispositivos._

### <a name="q-im-encountering-a-role-related-error-error-message-in-azure-portal-or-a-403-api-error"></a>P: Estou a deparar-me com um erro relacionado com a função (mensagem de erro no portal Azure ou um erro de API 403).
_Pode não ter permissões de acesso configuradas corretamente. Certifique-se de que configura as permissões de acesso corretamente de acordo com a documentação do [controlo de acesso de Atualização](./device-update-control-access.md) do Dispositivo._

### <a name="q-im-encountering-a-500-type-error-when-importing-content-to-the-device-update-service"></a>P: Estou a encontrar um erro de 500 tipos ao importar conteúdo para o serviço de Atualização de Dispositivos.
_Um código de erro na gama 500 pode indicar um problema com o serviço de Atualização do Dispositivo. Por favor, espere 5 minutos e tente de novo. Se o mesmo erro persistir, siga as instruções na secção [De Suporte do Microsoft](#contact) para apresentar um pedido de suporte com a Microsoft._

### <a name="q-im-encountering-an-error-code-when-importing-content-and-would-like-to-parse-it"></a>P: Estou a encontrar um código de erro ao importar conteúdo e gostaria de analisá-lo.
_Consulte a documentação dos [Códigos de Erro de Atualização](./device-update-error-codes.md) do Dispositivo para obter informações sobre códigos de erro de análise._

## <a name="device-failures"></a><a name="device-failure"></a>Falhas no dispositivo

### <a name="q-how-can-i-ensure-my-device-is-connected-to-device-update-for-iot-hub"></a>P: Como posso garantir que o meu dispositivo está ligado à Atualização do Dispositivo para o IoT Hub?
_Pode verificar se o seu dispositivo está ligado à Atualização do Dispositivo, verificando se aparece na secção de dispositivos "Não agrupados" na visão de conformidade do portal Azure._

### <a name="q-one-or-more-of-my-devices-is-failing-to-update"></a>P: Um ou mais dos meus dispositivos não estão a atualizar.
_Existem muitas causas de raiz possíveis para uma falha de atualização do dispositivo. Por favor, valide que o dispositivo está: 1) ligado à sua instância IoT Hub, 2) ligado à sua instância de Atualização do Dispositivo, e 3) o serviço de Otimização de Entrega (DO) está em execução. Se os três forem verdadeiros para o seu dispositivo, siga as instruções na secção [de Suporte do Microsoft](#contact) para apresentar um pedido de suporte com a Microsoft._

## <a name="deploying-an-update"></a><a name="deploy"></a> Implementação de uma atualização

### <a name="q-ive-deployed-an-update-to-my-devices-but-the-compliance-status-says-it-isnt-on-the-latest-update-what-should-i-do"></a>P: Implementei uma atualização para os meus dispositivos, mas o estado de conformidade diz que não está na última atualização. O que devo fazer?
_O estado de conformidade do dispositivo pode demorar até 5 minutos para ser atualizado. Por favor, espere, e depois verifique de novo._
### <a name="q-my-devices-deployment-status-shows-incompatible-what-should-i-do"></a>P: O estado de implantação do meu dispositivo mostra incompatível, o que devo fazer?
_As propriedades do fabricante e do modelo de um dispositivo direcionado podem ter sido alteradas após a ligação do dispositivo ao IoT Hub, fazendo com que o dispositivo seja agora considerado incompatível com o conteúdo da atualização da atual implementação._

_Verifique a [Interface Core da ADU](./device-update-plug-and-play) para ver que fabricante e modelo o seu dispositivo está a reportar ao serviço de Atualização do Dispositivo e certifique-se de que corresponde ao fabricante e modelo especificado no manifesto de [importação](./import-concepts.md) do conteúdo da atualização que está a ser implementado. Pode alterar estas propriedades para um determinado dispositivo utilizando o [ficheiro de configuração de atualização](./device-update-cofiguration-file.md)do dispositivo ._

### <a name="q-i-see-my-deployment-is-in-active-stage-but-none-of-my-devices-are-in-progress-with-the-update-what-should-i-do"></a>P: Vejo que a minha implementação está em fase "Ative", mas nenhum dos meus dispositivos está "em progresso" com a atualização. O que devo fazer?
_Certifique-se de que a data de início da sua implantação não está definida no futuro. Quando cria uma nova implementação, a data de início de implementação é incumprida para o dia seguinte como uma salvaguarda, a menos que a altere explicitamente. Pode esperar pela data de início da implementação ou cancelar a implementação em curso e criar uma nova implementação com a data de início desejada._

### <a name="q-im-trying-to-group-my-devices-but-i-dont-see-the-tag-in-the-drop-down-when-creating-a-group"></a>P: Estou a tentar agrupar os meus dispositivos, mas não vejo a etiqueta na entrega quando se cria um grupo.
_Certifique-se de que configura corretamente as rotas de mensagem no seu Hub IoT de acordo com a documentação de [recursos de Atualização](./device-update-resources.md) de Dispositivos. Terá de marcar novamente o seu dispositivo depois de configurar a rota._

_Outra causa de raiz pode ser o facto de ter aplicado a etiqueta antes de ligar o seu dispositivo à Atualização do Dispositivo para o IoT Hub. Certifique-se de que o seu dispositivo já está ligado à Atualização do Dispositivo. Pode verificar se o seu dispositivo está ligado à Atualização do Dispositivo para o IoT Hub, verificando se aparece em dispositivos "Não agrupados" na vista de conformidade. Adicione temporariamente uma etiqueta de valor diferente e, em seguida, adicione a etiqueta pretendida novamente assim que o dispositivo estiver ligado._

_Se estiver a utilizar o Serviço de Provisionamento de Dispositivos (DPS), certifique-se de que marca os seus dispositivos depois de serem a provisionados e não durante o processo de criação do Dispositivo. Se já marcou o seu dispositivo durante o passo de criação do Dispositivo, terá de marcar temporariamente o seu dispositivo com um valor diferente depois de ser provisionado e, em seguida, adicionar novamente a sua etiqueta pretendida._

### <a name="q-my-deployment-completed-successfully-but-some-devices-failed-to-update"></a>P: A minha implementação foi concluída com sucesso, mas alguns dispositivos não atualizaram.
_Isto pode ter sido causado por um erro do lado do cliente nos dispositivos falhados. Consulte a secção de falhas do dispositivo deste guia de resolução de problemas._

### <a name="q-i-encountered-an-error-in-the-ux-when-trying-to-initiate-a-deployment"></a>P: Deparei-me com um erro no UX ao tentar iniciar uma implantação.
_Isto pode ter sido causado por um bug de serviço/UX, ou por um problema de permissões da API. Por favor, siga as instruções na secção [De Suporte do Microsoft em contacto](#contact) para apresentar um pedido de suporte com a Microsoft._

### <a name="q-i-started-a-deployment-but-it-isnt-reaching-an-end-state"></a>P: Iniciei uma implantação, mas não está a chegar a um estado final.
_Isto pode ter sido causado por um problema de desempenho de serviço, um erro de serviço ou um erro do cliente. Por favor, re-preenrção após 10 minutos. Se encontrar o mesmo problema, por favor, puxe os registos do dispositivo e consulte a secção de Falhas do Dispositivo deste guia de resolução de problemas. Se o mesmo problema persistir, siga as instruções na secção [De Suporte do Microsoft](#contact) para apresentar um pedido de suporte com a Microsoft._

## <a name="downloading-updates-onto-devices"></a><a name="download"></a> Descarregar atualizações em dispositivos

### <a name="q-how-do-i-resume-a-download-when-a-device-has-reconnected-after-a-period-of-disconnection"></a>P: Como posso retomar um download quando um dispositivo se reconectou após um período de desconexão?
_O download será auto-retomado quando a conectividade for restaurada dentro de um período de 24 horas. Após 24 horas, o download terá de ser reiniciado pelo utilizador._
## <a name="using-microsoft-connected-cache-mcc"></a><a name="mcc"></a> Utilizando a Cache Conectada da Microsoft (MCC)

### <a name="q-i-am-encountering-an-issue-when-attempting-to-deploy-the-mcc-module-on-my-iot-edge-device"></a>P: Estou a deparar-me com um problema ao tentar implantar o módulo MCC no meu dispositivo IoT Edge.
_Consulte a [documentação IoT Edge]() para a implantação de módulos Edge em dispositivos IoT Edge. Pode verificar se o módulo MCC está a funcionar com sucesso no seu dispositivo IoT Edge navegando para http://localhost:5100/Summary ._
### <a name="q-one-of-my-iot-devices-is-attempting-to-download-an-update-through-mcc-but-is-failing"></a>P: Um dos meus dispositivos IoT está a tentar descarregar uma atualização através do MCC, mas está a falhar.
_Existem vários problemas que podem estar a causar falhas num dispositivo IoT na ligação à MCC. Para diagnosticar o problema, por favor, recolha os registos do cliente DO e do Nginx do dispositivo de falha (consulte a secção [de Suporte do Microsoft contactando](#contact) para obter instruções sobre a recolha de registos de clientes)._

_O seu dispositivo pode não conseguir retirar conteúdo da Internet para passar para o seu módulo MCC porque o URL que está a usar não é permitido. Para determinar se sim, terá de verificar as variáveis ambientais IoT Edge no portal Azure._
## <a name="contacting-microsoft-support"></a><a name="contact"></a> Contato com o Suporte da Microsoft

Se encontrar problemas que não possam ser resolvidos usando as FAQs acima, pode apresentar um pedido de suporte com o Microsoft Support através da interface do portal Azure. Dependendo da categoria a que indica que o seu problema pertence, poderá ser-lhe pedido que recolha e partilhe dados adicionais para ajudar o Microsoft Support a investigar o seu problema. 

Consulte abaixo as instruções sobre como recolher cada tipo de dados. Pode utilizar [o GetDevices]() para verificar se há informações adicionais na resposta à carga útil da API.

Além disso, as seguintes informações podem ser úteis para reduzir a causa principal do seu problema:
* Que tipo de dispositivo está a tentar atualizar (Azure Percept, IoT Edge Gateway, outros)
* Que tipo de cliente de Atualização de Dispositivos está a usar (Baseado em imagem, baseado em pacotes, simulador)
* O que o seu dispositivo está a executar
* Detalhes sobre a arquitetura do seu dispositivo
* Se utilizou com sucesso a Atualização do Dispositivo para atualizar um dispositivo antes

Se tiver alguma das informações acima disponíveis, por favor inclua-a na sua descrição do problema.

### <a name="collecting-client-logs"></a>Recolha de registos de clientes

* No Dispositivo Raspberry Pi existem dois conjuntos de troncos encontrados aqui:

    ```markdown
    /adu/logs
    ```

    ```markdown
    /var/cache/do-client-lite/log
    ```

* Para o cliente embalado, os registos são encontrados aqui:

    ```markdown
    /var/log/adu
    ```

    ```markdown
    /var/cache/do-client-lite/log
    ```

* Para o Simulador, os registos são encontrados aqui:

    ```markdown
    /tmp/aduc-logs
    ```

### <a name="error-codes"></a>Códigos de erro
Pode ser-lhe pedido que forneça códigos de erro ao reportar um problema relacionado com a importação de uma atualização, uma falha do dispositivo ou a implementação de uma atualização.

Os códigos de erro podem ser obtidos olhando para a interface [ADUCoreInterface.](./device-update-plug-and-play.md) Consulte a documentação dos [códigos de erro de atualização](./device-update-error-codes.md) do dispositivo para obter informações sobre como analisar códigos de erro para autodiagnóstico e resolução de problemas.

### <a name="trace-id"></a>ID de vestígios
Pode ser-lhe pedido que forneça um ID de rastreamento ao reportar um problema relacionado com a importação ou implementação de uma atualização.

O ID de vestígios para uma determinada ação de utilizador pode ser encontrado dentro da resposta da API, ou na secção De Histórico de Importação da interface de utilizador do portal Azure. 

Atualmente, os IDs de rastreio para ações de implantação só são acessíveis através da resposta da API.

### <a name="deployment-id"></a>ID de implantação
Pode ser-lhe pedido que forneça um ID de implementação ao reportar um problema relacionado com a implementação de uma atualização.

O ID de implementação é criado pelo utilizador quando chama a API para iniciar uma implementação.

Atualmente, os IDs de implementação para implementações iniciadas a partir da interface de utilizador do portal Azure são gerados automaticamente e não surgidos para o utilizador.

### <a name="iot-hub-instance-name"></a>Nome de instância do IoT Hub
Pode ser-lhe pedido que forneça o nome da sua instância IoT Hub ao reportar um problema relacionado com falhas do dispositivo ou implementando uma atualização.

O nome IoT Hub é escolhido pelo utilizador quando for provisionado pela primeira vez.

### <a name="device-update-account-name"></a>Nome da conta de atualização do dispositivo
Pode ser-lhe pedido que forneça o nome da sua conta de Atualização de Dispositivos ao reportar um problema relacionado com a importação de uma atualização, falhas do dispositivo ou implementação de uma atualização.

O nome da conta 'Atualização do Dispositivo' é escolhido pelo utilizador quando se inscreveu pela primeira vez no serviço. Mais informações podem ser encontradas na documentação de [recursos de Atualização](./device-update-resources.md) de Dispositivos.

### <a name="device-update-instance-name"></a>Nome da instância de atualização do dispositivo
Pode ser-lhe pedido que forneça o nome da sua instância de Atualização do Dispositivo ao reportar um problema relacionado com a importação de uma atualização, falhas do dispositivo ou implementação de uma atualização.

O nome da instância de atualização do dispositivo é escolhido pelo utilizador quando for forvisionado pela primeira vez. Mais informações podem ser encontradas na documentação de [recursos de Atualização](./device-update-resources.md) de Dispositivos.

### <a name="device-id"></a>ID do Dispositivo
Pode ser-lhe pedido que forneça um ID do dispositivo ao reportar um problema relacionado com falhas do dispositivo ou implementando uma atualização.

O ID do dispositivo é definido pelo cliente quando o dispositivo é for provisionado pela primeira vez. Também pode ser recuperado do dispositivo Twin.

### <a name="update-id"></a>ID de atualização
Pode ser-lhe pedido que forneça um ID de atualização ao reportar um problema relacionado com a implementação de uma atualização.

O ID de atualização é definido pelo cliente ao iniciar uma implementação.

### <a name="nginx-logs"></a>Troncos Nginx
Pode ser-lhe pedido que forneça registos Nginx ao reportar um problema relacionado com a Cache Conectada ao Microsoft.

### <a name="adu-conftxt"></a>ADU-conf.txt
Pode ser-lhe pedido que forneça o ficheiro de configuração de Atualização do Dispositivo ("adu-conf.txt") ao reportar um problema relacionado com a implementação de uma atualização.

O ficheiro de configuração é opcional e criado pelo utilizador seguindo as instruções na documentação de [configuração de atualização](./device-update-configuration-file.md) do dispositivo.

### <a name="import-manifest"></a>Manifesto de importação
Pode ser-lhe pedido que forneça o seu ficheiro manifesto de importação ao reportar um problema relacionado com a importação ou implementação de uma atualização.

O manifesto de importação é um ficheiro criado pelo cliente ao importar conteúdo de atualização para o serviço de Atualização de Dispositivos.

**[Próximo passo: Saiba mais sobre códigos de erro de atualização de dispositivos](.\device-update-error-codes.md)**