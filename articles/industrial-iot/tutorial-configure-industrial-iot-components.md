---
title: Configure os componentes Azure Industrial IoT
description: Neste tutorial, aprende-se a alterar os valores padrão da configuração.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 3/22/2021
ms.openlocfilehash: 348276a71b2227063374da6455445118fcb00fcf
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104787750"
---
# <a name="tutorial-configure-the-industrial-iot-components"></a>Tutorial: Configurar os componentes IoT Industriais

O script de implementação configura automaticamente todos os componentes para trabalhar em funcionamento entre si utilizando valores predefinidos. No entanto, as definições dos valores predefinidos podem ser alteradas para satisfazer os seus requisitos.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Personalizar a configuração dos componentes


Aqui estão algumas das definições de personalização mais relevantes para os componentes:
* IoT Hub
    * Acesso em rede→Pública: Acesso à Internet Configure, por exemplo, filtros IP
    * Ligações de ponto final → privado: Criar um ponto final que não seja acessível através da Internet e que possa ser consumido internamente por outros serviços Azure ou dispositivos no local (por exemplo, através de uma ligação VPN)
    * IoT Edge: Gerir a configuração dos dispositivos de borda que estão ligados aos servidores OPC UA 
* Cosmos DB
    * Replicar dados a nível global: Configurar a redundância de dados
    * Firewall e redes virtuais: Configurar o acesso à Internet e VNET e filtros IP
    * Conexões de ponto final privado: Criar um ponto final que não seja acessível através da Internet 
* Key Vault
    * Segredos: Gerir configurações da plataforma
    * Políticas de acesso: Gerir quais aplicações e utilizadores podem aceder aos dados no Cofre-Chave e quais as operações (por exemplo, ler, escrever, listar, excluir) que podem realizar na rede, firewall, VNET e pontos finais privados
* Registos Azure Ative (AAD)→App
    * <APP_NAME> web → Autenticação: Gerir URIs de resposta, que é a lista de URIs que podem ser usados como páginas de aterragem após a autenticação ter sucesso. O script de implementação pode não ser capaz de configurar isto automaticamente em certos cenários, tais como a falta de direitos de administração da AAD. Pode querer adicionar ou modificar URIs ao alterar o nome de anfitrião da aplicação Web, por exemplo, o número de porta utilizado pelo local para depurar
* Serviço de Aplicações
    * Configuração: Gerir as variáveis ambientais que controlam os serviços ou UI
* Máquina virtual
    * Networking: Configurar redes suportadas e regras de firewall
    * Consola em série: Acesso SSH para obter insights ou para depuração, obter as credenciais a partir da saída do script de implementação ou redefinir a palavra-passe
* IoT Hub → IoT Edge
    * Gerir as identidades dos dispositivos IoT Edge que podem aceder ao hub, configurar quais os módulos instalados e qual a configuração que utilizam, por exemplo, parâmetros de codificação para o Editor OPC
* IoT Hub → IoT Edge → \<DEVICE> → Conjunto de Módulos → OpcPublisher (apenas para operação autónoma do Editor OPC)


## <a name="configuration-options"></a>Opções de configuração

|Opção de configuração (abreviatura/nome completo)    |    Descrição   |
|----------------------------------------------|------------------|
pf/publishfile |O nome de arquivo para configurar os nós para publicar. Se esta opção for especificada, coloca o Editor OPC em modo autónomo.
Se/logfile |O nome de ficheiro do ficheiro de registo para utilizar.
ll/loglevel |O nível de registo a utilizar (permitido: fatal, erro, aviso, informação, depurado, verboso).
me/messageencoding |A codificação de mensagens para mensagens de saída permitiu valores: Json, Uadp
mm/mensagensmode |O modo de mensagens para mensagens de saída permitiu valores: PubSub, Samples
fm/fullfeaturedmessage |O modo completo em destaque para mensagens (todos os campos preenchidos). O padrão é 'verdadeiro', pois a compatibilidade do legado é 'falsa'
aa/autoaccept |A editora confiou em todos os servidores que é uma ligação com
bs/lotsize |O número de mensagens de alteração de dados da OPC UA a ser em cache para o loteamento.
si/iothubsendinterval |O intervalo de loteamento do gatilho em segundos.
ms/iothubmessagesize |O tamanho máximo da mensagem (IoT D2C).
om/maxoutgresssamsages |O tamanho máximo do tampão de saída de mensagem (IoT D2C).
di/diagnósticointerval |Mostra a informação de diagnóstico do editor no intervalo especificado em segundos (precisa de informação de nível de registo). -1 desativa o registo de diagnóstico remoto e a saída de diagnóstico
lt/logflugtimespan |O tempo em segundos quando o ficheiro de registo deve ser lavado.
ih/iothubprotocol |Protocolo para usar para comunicação com o centro. Valores permitidos: AmqpOverTcp, AmqpOverWebsocket, MqttOverTcp, MqttOverWebsocket, Amqp, Mqtt, Tcp, Websocket, Any
hb/heartbeatinterval |O editor está a usar isto como valor predefinido em segundos para a definição do intervalo do batimento cardíaco dos nós sem uma definição de intervalo de batimentocardíaco.
ot/tempo de operação |O tempo de operação do cliente da editora OPC UA na MS.
ol/opcmaxstringlen |O comprimento máximo de um opc de corda pode transmitir/receber.
oi/opcsamplinginterval |Valor predefinido em milissegundos para solicitar aos servidores valores de amostra
op/opcpublishinginterval |Valor predefinido em milissegundos para a definição de intervalo de publicação das subscrições contra o servidor OPC UA.
ct/criassiontimeout |O intervalo que a editora está a enviar mantém mensagens vivas em segundos para os servidores OPC nos pontos finais a que está ligada.
kt/keepalivethresholt |Especifique o número de pacotes de manutenção vivo que um servidor pode perder, antes de a sessão ser desligada.
tm/trustmyself |O certificado de editor é colocado automaticamente na loja de confiança.
at/appcertstoretype |O próprio tipo de loja cert de aplicação (permitido: Diretório, Loja X509).


## <a name="next-steps"></a>Passos seguintes
Agora que aprendeu a alterar os valores padrão da configuração, pode 

> [!div class="nextstepaction"]
> [Puxe os dados do IIoT para o ADX](tutorial-industrial-iot-azure-data-explorer.md)

> [!div class="nextstepaction"]
> [Visualizar e analisar os dados utilizando insights da Série De Tempo](tutorial-visualize-data-time-series-insights.md)
