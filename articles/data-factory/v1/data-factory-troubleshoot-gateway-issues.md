---
title: Resolver problemas do Data Management Gateway | Documentos da Microsoft
description: Fornece sugestões para resolver problemas relacionados com o Data Management Gateway.
services: data-factory
author: nabhishek
manager: craigg
ms.assetid: c6756c37-4e5a-4d1e-ab52-365f149b4128
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/01/2017
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 0559d89bd691323a95713d518df05e58283cef39
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58119348"
---
# <a name="troubleshoot-issues-with-using-data-management-gateway"></a>Utilizar o Data Management Gateway para resolver problemas
Este artigo fornece informações sobre como resolver problemas com o Data Management Gateway.

> [!NOTE]
> Este artigo aplica-se à versão 1 do Azure Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, veja [Self-hosted integration runtime no Data Factory](../create-self-hosted-integration-runtime.md).

Consulte a [Data Management Gateway](data-factory-data-management-gateway.md) artigo para obter informações detalhadas sobre o gateway. Consulte a [mover dados entre locais e na cloud](data-factory-move-data-between-onprem-and-cloud.md) artigo para obter instruções de mover dados de uma base de dados do SQL Server no local para o armazenamento de Blobs do Microsoft Azure ao utilizar o gateway.

## <a name="failed-to-install-or-register-gateway"></a>Falha ao instalar ou registar gateway
### <a name="1-problem"></a>1. Problema
Verá esta mensagem de erro ao instalar e registar um gateway, especificamente, ao transferir o ficheiro de instalação do gateway.

`Unable to connect to the remote server". Please check your local settings (Error Code: 10003).`

#### <a name="cause"></a>Causa
A máquina em que está a tentar instalar o gateway não conseguiu transferir o ficheiro de instalação do gateway mais recente a partir do Centro de transferências devido a um problema de rede.

#### <a name="resolution"></a>Resolução
Verifique as definições de servidor de proxy de firewall para ver se as definições de bloquear a ligação de rede do computador para o [Centro de transferências](https://download.microsoft.com/)e Atualize as definições em conformidade.

Em alternativa, pode transferir o ficheiro de instalação para o gateway mais recente a partir da [Centro de transferências](https://www.microsoft.com/download/details.aspx?id=39717) em outras máquinas que podem aceder ao centro de download. Pode, em seguida, copie o ficheiro de instalador para o computador do anfitrião de gateway e execute-o manualmente para instalar e atualizar o gateway.

### <a name="2-problem"></a>2. Problema
Se este erro ocorrer ao tentar instalar um gateway ao clicar em **instalar diretamente neste computador** no portal do Azure.

`Error:  Abort installing a new gateway on this computer because this computer has an existing installed gateway and a computer without any installed gateway is required for installing a new gateway.`  

#### <a name="cause"></a>Causa
Um gateway já está instalado na máquina.

#### <a name="resolution"></a>Resolução
Desinstalar o gateway existente no computador e clique nas **instalar diretamente neste computador** ligar novamente.

### <a name="3-problem"></a>3. Problema
Poderá ver este erro ao registar um novo gateway.

`Error: The gateway has encountered an error during registration.`

#### <a name="cause"></a>Causa
Poderá ver esta mensagem para um dos seguintes motivos:

* O formato da chave de gateway é inválido.
* A chave de gateway foi invalidada.
* A chave de gateway tenha sido regenerada a partir do portal.  

#### <a name="resolution"></a>Resolução
Certifique-se de que se estiver a utilizar a chave de gateway correto no portal. Se for necessário, voltar a gerar uma chave e utilizar a chave para registar o gateway.

### <a name="4-problem"></a>4. Problema
Poderá ver a seguinte mensagem de erro quando estiver a registar um gateway.

`Error: The content or format of the gateway key "{gatewayKey}" is invalid, please go to azure portal to create one new gateway or regenerate the gateway key.`



![Conteúdo ou formato da chave é inválido](media/data-factory-troubleshoot-gateway-issues/invalid-format-gateway-key.png)

#### <a name="cause"></a>Causa
O conteúdo ou o formato da chave do gateway de entrada é incorreto. Um dos motivos pelos quais é possível que copiou apenas uma parte da chave a partir do portal do ou estiver a utilizar uma chave inválida.

#### <a name="resolution"></a>Resolução
Gerar uma chave de gateway no portal e utilize o botão de cópia para copiar a chave de toda. Em seguida, cole-o nesta janela para registar o gateway.

### <a name="5-problem"></a>5. Problema
Poderá ver a seguinte mensagem de erro quando estiver a registar um gateway.

`Error: The gateway key is invalid or empty. Specify a valid gateway key from the portal.`

![Chave de gateway é inválido ou está vazio](media/data-factory-troubleshoot-gateway-issues/gateway-key-is-invalid-or-empty.png)

#### <a name="cause"></a>Causa
A chave de gateway tenha sido regenerada ou o gateway tiver sido eliminado no portal do Azure. Também pode acontecer se a configuração do Gateway de gestão de dados não está mais recente.

#### <a name="resolution"></a>Resolução
Verifique se o programa de configuração do Gateway de gestão de dados é a versão mais recente, pode encontrar a versão mais recente no Microsoft [Centro de transferências](https://go.microsoft.com/fwlink/p/?LinkId=271260).

Se a configuração é atual / mais recente e o gateway ainda existe no Portal, regenerar a chave de gateway no portal do Azure, utilize o botão de cópia para copiar a chave toda e, em seguida, colá-lo nesta janela para registar o gateway. Caso contrário, recriar o gateway e começar de novo.

### <a name="6-problem"></a>6. Problema
Poderá ver a seguinte mensagem de erro quando estiver a registar um gateway.

`Error: Gateway has been online for a while, then shows “Gateway is not registered” with the status “Gateway key is invalid”`

![Chave de gateway é inválido ou está vazio](media/data-factory-troubleshoot-gateway-issues/gateway-not-registered-key-invalid.png)

#### <a name="cause"></a>Causa
Este erro pode acontecer porque o gateway foi eliminado ou tenha sido regenerada a chave de gateway associado.

#### <a name="resolution"></a>Resolução
Se o gateway tiver sido eliminado, voltar a criar o gateway a partir do portal, clique em **registar**, copie a chave a partir do portal, cole-o e tente registar o gateway.

Se o gateway ainda existe, mas tenha sido regenerada a respetiva chave, utilize a nova chave para registar o gateway. Se não tiver a chave, regenere a chave novamente no portal.

### <a name="7-problem"></a>7. Problema
Quando estiver a registar um gateway, poderá ter de introduzir o caminho e a palavra-passe para um certificado.

![Especifique o certificado](media/data-factory-troubleshoot-gateway-issues/specify-certificate.png)

#### <a name="cause"></a>Causa
O gateway foi registado em outras máquinas antes. Durante o registo inicial de um gateway, um certificado de encriptação foi associado com o gateway. O certificado pode ser Self-gerado pelo gateway ou fornecido pelo usuário.  Este certificado é utilizado para encriptar as credenciais do armazenamento de dados (serviço ligado).  

![Exportar o certificado](media/data-factory-troubleshoot-gateway-issues/export-certificate.png)

Ao restaurar o gateway num computador anfitrião diferente, o Assistente de registo solicita este certificado desencriptar credenciais anteriormente criptografadas com este certificado.  Sem este certificado, as credenciais não podem ser desencriptadas pelo novo gateway e execuções de atividade de cópia subsequentes associadas com este novo gateway irão falhar.  

#### <a name="resolution"></a>Resolução
Se exportou o certificado da credencial da máquina de gateway original utilizando o **exportar** botão a **definições** separador no Data Management Gateway Configuration Manager, utilize o certificado aqui.

Não pode ignorar este estágio ao recuperar um gateway. Se o certificado está em falta, terá de eliminar o gateway a partir do portal e volte a criar um novo gateway.  Além disso, atualize todos os serviços ligados que estão relacionados com o gateway ao entrar novamente as respetivas credenciais.

### <a name="8-problem"></a>8. Problema
Poderá ver a seguinte mensagem de erro.

`Error: The remote server returned an error: (407) Proxy Authentication Required.`

#### <a name="cause"></a>Causa
Este erro ocorre quando o gateway estiver num ambiente que requer um proxy HTTP para aceder a recursos da Internet ou a palavra-passe de autenticação de seu proxy é alterado, mas não é atualizado em conformidade no seu gateway.

#### <a name="resolution"></a>Resolução
Siga as instruções na secção de considerações de servidor de Proxy deste artigo e configure as definições de proxy com o Data Management Gateway Configuration Manager.

## <a name="gateway-is-online-with-limited-functionality"></a>Gateway está online com funcionalidade limitada
### <a name="1-problem"></a>1. Problema
Ver o estado do gateway como online com funcionalidades limitadas.

#### <a name="cause"></a>Causa
Ver o estado do gateway como online com funcionalidade limitada para um dos seguintes motivos:

* Gateway não é possível ligar ao serviço cloud através do Azure Service Bus.
* Serviço em nuvem não é possível ligar ao gateway através do Service Bus.

Quando o gateway está online com funcionalidades limitadas, poderá não conseguir utilizar o Assistente de cópia do Data Factory para criar pipelines de dados para copiar dados de ou para arquivos de dados no local. Como solução, pode utilizar o Editor do Data Factory no portal, do Visual Studio ou do Azure PowerShell.

#### <a name="resolution"></a>Resolução
Resolução para este problema (online com funcionalidades limitadas) baseia-se o gateway não é possível ligar ao serviço cloud ou a outra forma. As secções seguintes fornecem essas resoluções.

### <a name="2-problem"></a>2. Problema
Consulte o seguinte erro.

`Error: Gateway cannot connect to cloud service through service bus`

![Gateway não é possível ligar ao serviço em nuvem](media/data-factory-troubleshoot-gateway-issues/gateway-cannot-connect-to-cloud-service.png)

#### <a name="cause"></a>Causa
Gateway não é possível ligar ao serviço cloud através do Service Bus.

#### <a name="resolution"></a>Resolução
Siga estes passos para obter o gateway online:

1. Permitir o endereço IP regras de saída no computador gateway e do firewall Corporativo. Pode localizar endereços IP a partir do registo de eventos do Windows (ID = = 401): Foi efetuada uma tentativa de aceder a um soquete de uma forma proibido por suas permissões de acesso XX. XX. XX. XX:9350.
1. Configure definições de proxy no gateway. Consulte a secção de considerações do servidor de Proxy para obter detalhes.
1. Ative as portas de saída 5671 e 9350 a 9354 em ambos os o Firewall do Windows no computador gateway e do firewall Corporativo. Consulte a secção de firewall para obter detalhes e de portas. Este passo é opcional, mas é recomendada para consideração de desempenho.

### <a name="3-problem"></a>3. Problema
Consulte o seguinte erro.

`Error: Cloud service cannot connect to gateway through service bus.`

#### <a name="cause"></a>Causa
Um erro transitório na conectividade de rede.

#### <a name="resolution"></a>Resolução
Siga estes passos para obter o gateway online:

1. Aguarde alguns minutos, a conectividade vai ser recuperada automaticamente quando o erro já não existe.
1. Se o erro persistir, reinicie o serviço de gateway.

## <a name="failed-to-author-linked-service"></a>Falha ao criar o serviço ligado
### <a name="problem"></a>Problema
Poderá ver este erro quando tenta utilizar o Gestor de credenciais no portal para inserir credenciais para um novo serviço ligado, ou atualizar as credenciais para um serviço ligado existente.

`Error: The data store '<Server>/<Database>' cannot be reached. Check connection settings for the data source.`

Quando vir este erro, a página de definições do Data Management Gateway Configuration Manager pode ser semelhante a captura de ecrã seguinte.

![Não é possível aceder a base de dados](media/data-factory-troubleshoot-gateway-issues/database-cannot-be-reached.png)

#### <a name="cause"></a>Causa
O certificado SSL pode ter sido perdido no computador gateway. O computador de gateway não é possível carregar o certificado atualmente que é utilizado para encriptação SSL. Também poderá ver uma mensagem de erro no registo de eventos que é semelhante à mensagem seguinte.

 `Unable to get the gateway settings from cloud service. Check the gateway key and the network connection. (Certificate with thumbprint cannot be loaded.)`

#### <a name="resolution"></a>Resolução
Siga estes passos para resolver o problema:

1. Inicie o Gestor de configuração do Data Management Gateway.
2. Mudar para o separador **Definições**.  
3. Clique nas **alterar** botão para alterar o certificado SSL.

   ![Botão de certificado de alteração](media/data-factory-troubleshoot-gateway-issues/change-button-ssl-certificate.png)
4. Selecione um novo certificado como o certificado SSL. Pode utilizar qualquer certificado SSL que é gerado por si ou de qualquer organização.

   ![Especifique o certificado](media/data-factory-troubleshoot-gateway-issues/specify-http-end-point.png)

## <a name="copy-activity-fails"></a>Atividade Copy falhar
### <a name="problem"></a>Problema
Pode observar a seguinte falha "UserErrorFailedToConnectToSqlserver" depois de configurar um pipeline no portal.

`Error: Copy activity encountered a user error: ErrorCode=UserErrorFailedToConnectToSqlServer,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Cannot connect to SQL Server`

#### <a name="cause"></a>Causa
Isto pode acontecer por diferentes motivos e atenuação varia de acordo.

#### <a name="resolution"></a>Resolução
Permita ligações de saída TCP através da porta TCP/1433 no lado do cliente de Gateway de gestão de dados antes de ligar a uma base de dados SQL.

Se a base de dados de destino é uma base de dados SQL do Azure, verifique as definições do SQL Server firewall para o Azure também.

Consulte a secção seguinte para testar a ligação ao arquivo de dados no local.

## <a name="data-store-connection-or-driver-related-errors"></a>Ligação de arquivo de dados ou erros relacionados com o controlador
Se vir armazenar ligação ou erros relacionados com o controlador de dados, conclua os seguintes passos:

1. Inicie o Gestor de configuração do Data Management Gateway no computador gateway.
2. Mude para o **diagnóstico** separador.
3. Na **Testar ligação**, adicione os valores de grupo do gateway.
4. Clique em **teste** para ver se pode ligar à origem de dados no local do computador gateway, utilizando as informações de ligação e credenciais. Depois de instalar um controlador, se a ligação de teste continuar a falhar, reinicie o gateway para que este possa escolher a mais recente.

![Testar ligação no separador de diagnóstico](media/data-factory-troubleshoot-gateway-issues/test-connection-in-diagnostics-tab.png)

## <a name="gateway-logs"></a>Registos do gateway
### <a name="send-gateway-logs-to-microsoft"></a>Enviar registos do gateway para a Microsoft
Quando contactar o Support da Microsoft para obter ajuda com problemas do gateway de resolução de problemas, poderá ser-lhe pedido para partilhar os registos do gateway. Com o lançamento do gateway, pode partilhar os registos do gateway necessário com dois cliques de botão no Data Management Gateway Configuration Manager.    

1. Mude para o **diagnóstico** separador no Data Management Gateway Configuration Manager.

    ![Separador de diagnóstico do Gateway de gestão de dados](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-diagnostics-tab.png)
2. Clique em **enviar registos** para ver a caixa de diálogo seguinte.

    ![Registos de gestão do Gateway de enviar dados](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-dialog.png)
3. (Opcional) Clique em **ver registos** para rever registos de eventos visualizador.
4. (Opcional) Clique em **privacidade** para rever a declaração de privacidade do Microsoft web services.
5. Quando estiver satisfeito com o que está prestes a carregar, clique em **enviar registos** para realmente enviar os registos dos últimos sete dias para a Microsoft para resolução de problemas. Deverá ver o estado da operação envie registos, conforme mostrado na captura de ecrã seguinte.

    ![Data Management Gateway enviar registos de estado](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-status.png)
6. Depois da operação estiver concluída, verá uma caixa de diálogo conforme mostrado na captura de ecrã seguinte.

    ![Data Management Gateway enviar registos de estado](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-result.png)
7. Guardar a **ID do relatório** e partilhá-lo com Support da Microsoft. O ID de relatório é utilizado para localizar os registos do gateway que carregou para resolução de problemas.  O ID do relatório também é guardado de evento Visualizador.  Pode encontrá-lo ao olhar para o ID de evento "25" e verificar a data e hora.

    ![ID do relatório de registos de envio de Gateway de gestão de dados](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-report-id.png)    

### <a name="archive-gateway-logs-on-gateway-host-machine"></a>Registos do gateway de arquivo no computador do anfitrião de gateway
Existem alguns cenários em que tiver problemas de gateway e não é possível partilhar registos do gateway diretamente:

* Se instalar manualmente o gateway e registar o gateway.
* Tente registar o gateway com uma chave regenerada na Data Management Gateway Configuration Manager.
* Tente enviar os registos e não é possível ligar o serviço de anfitrião de gateway.

Nestes cenários, pode guardar os registos do gateway como um ficheiro zip e partilhá-lo quando contactar o suporte da Microsoft. Por exemplo, se receber um erro ao registar o gateway, como mostrado na captura de ecrã seguinte.   

![Erro de registo do Gateway de gestão de dados](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-registration-error.png)

Clique nas **arquivar registos do gateway** ligar a arquivar e guarde os registos e, em seguida, partilhar o ficheiro zip com suporte da Microsoft.

![Registos de arquivo de Gateway de gestão de dados](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-archive-logs.png)

### <a name="locate-gateway-logs"></a>Localizar registos do gateway
Pode encontrar informações de registo do gateway detalhadas nos registos de eventos do Windows.

1. Iniciar o Windows **Visualizador de eventos**.
2. Localize os registos no **registos de serviços e aplicativos** > **Data Management Gateway** pasta.

   Quando estiver a resolver problemas relacionados com o gateway de problemas, procure eventos de nível de erro de evento Visualizador.

![Registos do Gateway de gestão de dados no Visualizador de eventos](media/data-factory-troubleshoot-gateway-issues/gateway-logs-event-viewer.png)
