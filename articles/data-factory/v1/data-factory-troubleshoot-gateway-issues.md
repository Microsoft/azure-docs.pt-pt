---
title: Solucionar problemas de Gerenciamento de Dados gateway
description: Fornece dicas para solucionar problemas relacionados ao Gerenciamento de Dados gateway.
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
ms.openlocfilehash: 34930061189c11c9cea9c2cd0feb2ede9aade74d
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73682327"
---
# <a name="troubleshoot-issues-with-using-data-management-gateway"></a>Utilizar o Data Management Gateway para resolver problemas
Este artigo fornece informações sobre como solucionar problemas com o uso de Gerenciamento de Dados gateway.

> [!NOTE]
> Este artigo aplica-se à versão 1 do Azure Data Factory. Se você estiver usando a versão atual do serviço de Data Factory, consulte [tempo de execução de integração auto-hospedado no data Factory](../create-self-hosted-integration-runtime.md).

Consulte o artigo [Gerenciamento de dados gateway](data-factory-data-management-gateway.md) para obter informações detalhadas sobre o gateway. Consulte o artigo [mover dados entre o local e a nuvem](data-factory-move-data-between-onprem-and-cloud.md) para obter uma explicação sobre como mover dados de um banco de SQL Server local para Microsoft Azure armazenamento de BLOBs usando o gateway.

## <a name="failed-to-install-or-register-gateway"></a>Falha ao instalar ou registrar o gateway
### <a name="1-problem"></a>1. problema
Você vê essa mensagem de erro ao instalar e registrar um gateway, especificamente, ao baixar o arquivo de instalação do gateway.

`Unable to connect to the remote server". Please check your local settings (Error Code: 10003).`

#### <a name="cause"></a>Causa
O computador no qual você está tentando instalar o gateway falhou ao baixar o arquivo de instalação do gateway mais recente do centro de download devido a um problema de rede.

#### <a name="resolution"></a>Resolução
Verifique as configurações do servidor proxy do firewall para ver se as configurações bloqueiam a conexão de rede do computador com o [centro de download](https://download.microsoft.com/)e atualize as configurações de acordo.

Como alternativa, você pode baixar o arquivo de instalação para o gateway mais recente do [centro de download](https://www.microsoft.com/download/details.aspx?id=39717) em outros computadores que podem acessar o centro de download. Em seguida, você pode copiar o arquivo do instalador para o computador host do gateway e executá-lo manualmente para instalar e atualizar o gateway.

### <a name="2-problem"></a>2. problema
Você verá esse erro quando estiver tentando instalar um gateway clicando em **instalar diretamente neste computador** na portal do Azure.

`Error:  Abort installing a new gateway on this computer because this computer has an existing installed gateway and a computer without any installed gateway is required for installing a new gateway.`  

#### <a name="cause"></a>Causa
Um gateway já está instalado no computador.

#### <a name="resolution"></a>Resolução
Desinstale o gateway existente no computador e clique no link **instalar diretamente neste computador** .

### <a name="3-problem"></a>3. problema
Você pode ver esse erro ao registrar um novo gateway.

`Error: The gateway has encountered an error during registration.`

#### <a name="cause"></a>Causa
Você pode ver essa mensagem por um dos seguintes motivos:

* O formato da chave do gateway é inválido.
* A chave do gateway foi invalidada.
* A chave do gateway foi regenerada no Portal.  

#### <a name="resolution"></a>Resolução
Verifique se você está usando a chave de gateway correta do Portal. Se necessário, gere novamente uma chave e use a chave para registrar o gateway.

### <a name="4-problem"></a>4. problema
Você poderá ver a seguinte mensagem de erro quando estiver registrando um gateway.

`Error: The content or format of the gateway key "{gatewayKey}" is invalid, please go to azure portal to create one new gateway or regenerate the gateway key.`



![O conteúdo ou o formato da chave é inválido](media/data-factory-troubleshoot-gateway-issues/invalid-format-gateway-key.png)

#### <a name="cause"></a>Causa
O conteúdo ou o formato da chave do gateway de entrada está incorreto. Uma das razões pode ser que você copiou apenas uma parte da chave do portal ou está usando uma chave inválida.

#### <a name="resolution"></a>Resolução
Gere uma chave de gateway no portal e use o botão de cópia para copiar a chave inteira. Em seguida, Cole-a nesta janela para registrar o gateway.

### <a name="5-problem"></a>5. problema
Você poderá ver a seguinte mensagem de erro quando estiver registrando um gateway.

`Error: The gateway key is invalid or empty. Specify a valid gateway key from the portal.`

![A chave do gateway é inválida ou está vazia](media/data-factory-troubleshoot-gateway-issues/gateway-key-is-invalid-or-empty.png)

#### <a name="cause"></a>Causa
A chave do gateway foi regenerada ou o gateway foi excluído no portal do Azure. Isso também pode acontecer se a configuração do gateway de Gerenciamento de Dados não for a mais recente.

#### <a name="resolution"></a>Resolução
Verifique se a instalação do gateway de Gerenciamento de Dados é a versão mais recente, você pode encontrar a versão mais recente no [centro de download](https://go.microsoft.com/fwlink/p/?LinkId=271260)da Microsoft.

Se a instalação for atual/mais recente e o gateway ainda existir no portal, gere novamente a chave do gateway no portal do Azure e use o botão Copiar para copiar a chave inteira e, em seguida, Cole-a nesta janela para registrar o gateway. Caso contrário, recrie o gateway e comece novamente.

### <a name="6-problem"></a>6. problema
Você poderá ver a seguinte mensagem de erro quando estiver registrando um gateway.

`Error: Gateway has been online for a while, then shows “Gateway is not registered” with the status “Gateway key is invalid”`

![A chave do gateway é inválida ou está vazia](media/data-factory-troubleshoot-gateway-issues/gateway-not-registered-key-invalid.png)

#### <a name="cause"></a>Causa
Esse erro pode ocorrer porque o gateway foi excluído ou a chave do gateway associada foi regenerada.

#### <a name="resolution"></a>Resolução
Se o gateway tiver sido excluído, recrie o gateway no portal, clique em **registrar**, copie a chave no portal, Cole-a e tente registrar o gateway.

Se o gateway ainda existir, mas sua chave tiver sido regenerada, use a nova chave para registrar o gateway. Se você não tiver a chave, regenere a chave novamente no Portal.

### <a name="7-problem"></a>7. problema
Quando você estiver registrando um gateway, talvez seja necessário inserir o caminho e a senha de um certificado.

![Especificar certificado](media/data-factory-troubleshoot-gateway-issues/specify-certificate.png)

#### <a name="cause"></a>Causa
O gateway foi registrado em outros computadores antes de. Durante o registro inicial de um gateway, um certificado de criptografia foi associado ao gateway. O certificado pode ser gerado automaticamente pelo gateway ou fornecido pelo usuário.  Esse certificado é usado para criptografar credenciais do armazenamento de dados (serviço vinculado).  

![Exportar o certificado](media/data-factory-troubleshoot-gateway-issues/export-certificate.png)

Ao restaurar o gateway em um computador host diferente, o assistente de registro solicita que esse certificado descriptografe as credenciais criptografadas anteriormente com esse certificado.  Sem esse certificado, as credenciais não poderão ser descriptografadas pelo novo gateway e as execuções de atividade de cópia subsequentes associadas a esse novo gateway falharão.  

#### <a name="resolution"></a>Resolução
Se você exportou o certificado de credencial do computador do gateway original usando o botão **Exportar** na guia **configurações** em gerenciamento de dados gateway Configuration Manager, use o certificado aqui.

Não é possível ignorar esse estágio ao recuperar um gateway. Se o certificado estiver ausente, você precisará excluir o gateway do portal e recriar um novo gateway.  Além disso, atualize todos os serviços vinculados relacionados ao gateway, reinserindo suas credenciais.

### <a name="8-problem"></a>8. problema
Você pode ver a seguinte mensagem de erro.

`Error: The remote server returned an error: (407) Proxy Authentication Required.`

#### <a name="cause"></a>Causa
Esse erro ocorre quando o gateway está em um ambiente que requer um proxy HTTP para acessar recursos da Internet, ou a senha de autenticação do proxy é alterada, mas não é atualizada de acordo em seu gateway.

#### <a name="resolution"></a>Resolução
Siga as instruções na seção considerações sobre o servidor proxy deste artigo e defina as configurações de proxy com Gerenciamento de Dados gateway Configuration Manager.

## <a name="gateway-is-online-with-limited-functionality"></a>O gateway está online com funcionalidade limitada
### <a name="1-problem"></a>1. problema
Você vê o status do gateway como online com funcionalidade limitada.

#### <a name="cause"></a>Causa
Você vê o status do gateway como online com funcionalidade limitada por um dos seguintes motivos:

* O gateway não pode se conectar ao serviço de nuvem por meio do barramento de serviço do Azure.
* O serviço de nuvem não pode se conectar ao gateway por meio do barramento de serviço.

Quando o gateway estiver online com funcionalidade limitada, talvez você não consiga usar o assistente de cópia Data Factory para criar pipelines de dados para copiar dados de ou para armazenamentos de dados locais. Como alternativa, você pode usar o editor de Data Factory no portal, no Visual Studio ou Azure PowerShell.

#### <a name="resolution"></a>Resolução
A resolução para esse problema (online com funcionalidade limitada) se baseia em se o gateway não pode se conectar ao serviço de nuvem ou à outra maneira. As seções a seguir fornecem essas resoluções.

### <a name="2-problem"></a>2. problema
Você verá o erro a seguir.

`Error: Gateway cannot connect to cloud service through service bus`

![O gateway não pode se conectar ao serviço de nuvem](media/data-factory-troubleshoot-gateway-issues/gateway-cannot-connect-to-cloud-service.png)

#### <a name="cause"></a>Causa
O gateway não pode se conectar ao serviço de nuvem por meio do barramento de serviço.

#### <a name="resolution"></a>Resolução
Siga estas etapas para colocar o gateway online novamente:

1. Permitir regras de saída de endereço IP no computador do gateway e no firewall corporativo. Você pode encontrar endereços IP no log de eventos do Windows (ID = = 401): foi feita uma tentativa de acessar um soquete de uma maneira proibida por suas permissões de acesso XX. XX. XX. XX: 9350.
1. Defina as configurações de proxy no gateway. Consulte a seção considerações do servidor proxy para obter detalhes.
1. Habilite as portas de saída 5671 e 9350-9354 no firewall do Windows no computador do gateway e no firewall corporativo. Consulte a seção portas e firewall para obter detalhes. Essa etapa é opcional, mas é recomendável para considerar o desempenho.

### <a name="3-problem"></a>3. problema
Você verá o erro a seguir.

`Error: Cloud service cannot connect to gateway through service bus.`

#### <a name="cause"></a>Causa
Um erro transitório na conectividade de rede.

#### <a name="resolution"></a>Resolução
Siga estas etapas para colocar o gateway online novamente:

1. Aguarde alguns minutos, a conectividade será recuperada automaticamente quando o erro for eliminado.
1. Se o erro persistir, reinicie o serviço de gateway.

## <a name="failed-to-author-linked-service"></a>Falha ao criar o serviço vinculado
### <a name="problem"></a>Problema
Você pode ver esse erro ao tentar usar o Gerenciador de credenciais no portal para inserir credenciais para um novo serviço vinculado ou atualizar as credenciais para um serviço vinculado existente.

`Error: The data store '<Server>/<Database>' cannot be reached. Check connection settings for the data source.`

Quando você vir esse erro, a página Configurações do Gerenciamento de Dados gateway Configuration Manager poderá ser parecida com a captura de tela a seguir.

![O banco de dados não pode ser alcançado](media/data-factory-troubleshoot-gateway-issues/database-cannot-be-reached.png)

#### <a name="cause"></a>Causa
O certificado SSL pode ter sido perdido no computador do gateway. O computador de gateway não pode carregar o certificado atualmente usado para criptografia SSL. Você também pode ver uma mensagem de erro no log de eventos semelhante à mensagem a seguir.

 `Unable to get the gateway settings from cloud service. Check the gateway key and the network connection. (Certificate with thumbprint cannot be loaded.)`

#### <a name="resolution"></a>Resolução
Siga estas etapas para resolver o problema:

1. Iniciar o Configuration Manager do gateway de Gerenciamento de Dados.
2. Mudar para o separador **Definições**.  
3. Clique no botão **alterar** para alterar o certificado SSL.

   ![Botão Alterar certificado](media/data-factory-troubleshoot-gateway-issues/change-button-ssl-certificate.png)
4. Selecione um novo certificado como o certificado SSL. Você pode usar qualquer certificado SSL gerado por você ou por qualquer organização.

   ![Especificar certificado](media/data-factory-troubleshoot-gateway-issues/specify-http-end-point.png)

## <a name="copy-activity-fails"></a>Falha na atividade de cópia
### <a name="problem"></a>Problema
Você pode observar a seguinte falha de "UserErrorFailedToConnectToSqlserver" depois de configurar um pipeline no Portal.

`Error: Copy activity encountered a user error: ErrorCode=UserErrorFailedToConnectToSqlServer,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Cannot connect to SQL Server`

#### <a name="cause"></a>Causa
Isso pode ocorrer por diferentes motivos, e a mitigação varia de acordo.

#### <a name="resolution"></a>Resolução
Permita conexões TCP de saída pela porta TCP/1433 no lado do cliente Gerenciamento de Dados gateway antes de se conectar a um banco de dados SQL.

Se o banco de dados de destino for um banco de dados SQL do Azure, verifique SQL Server configurações de firewall para o Azure também.

Consulte a seção a seguir para testar a conexão com o armazenamento de dados local.

## <a name="data-store-connection-or-driver-related-errors"></a>Erros relacionados à conexão do repositório de dados ou ao driver
Se você vir erros de conexão do repositório de dados ou relacionados ao driver, conclua as seguintes etapas:

1. Inicie o Configuration Manager do gateway de Gerenciamento de Dados no computador do gateway.
2. Alterne para a guia **diagnóstico** .
3. Em **testar conexão**, adicione os valores do grupo de gateway.
4. Clique em **testar** para ver se você pode se conectar à fonte de dados local do computador do gateway usando as informações e as credenciais de conexão. Depois de instalar um controlador, se a ligação de teste continuar a falhar, reinicie o gateway para que este possa escolher a mais recente.

![Testar conexão na guia diagnóstico](media/data-factory-troubleshoot-gateway-issues/test-connection-in-diagnostics-tab.png)

## <a name="gateway-logs"></a>Logs de gateway
### <a name="send-gateway-logs-to-microsoft"></a>Enviar logs de gateway para a Microsoft
Ao entrar em contato com Suporte da Microsoft para obter ajuda com a solução de problemas de gateway, você pode ser solicitado a compartilhar seus logs de gateway. Com o lançamento do gateway, você pode compartilhar os logs de gateway necessários com dois cliques de botão no Configuration Manager Gerenciamento de Dados gateway.    

1. Alterne para a guia **diagnóstico** no Configuration Manager gerenciamento de dados gateway.

    ![Guia diagnóstico de Gerenciamento de Dados gateway](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-diagnostics-tab.png)
2. Clique em **enviar logs** para ver a caixa de diálogo a seguir.

    ![Logs de envio de Gerenciamento de Dados gateway](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-dialog.png)
3. Adicional Clique em **Exibir logs** para examinar os logs no Visualizador de eventos.
4. Adicional Clique em **privacidade** para examinar a declaração de privacidade dos serviços Web da Microsoft.
5. Quando estiver satisfeito com o que você está prestes a carregar, clique em **enviar logs** para realmente enviar os logs dos últimos sete dias para a Microsoft para solução de problemas. Você deve ver o status da operação Send-logs, conforme mostrado na captura de tela a seguir.

    ![Status de logs de envio de Gerenciamento de Dados gateway](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-status.png)
6. Depois que a operação for concluída, você verá uma caixa de diálogo, conforme mostrado na captura de tela a seguir.

    ![Status de logs de envio de Gerenciamento de Dados gateway](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-result.png)
7. Salve a **ID do relatório** e compartilhe-a com suporte da Microsoft. A ID do relatório é usada para localizar os logs de gateway que você carregou para solução de problemas.  A ID do relatório também é salva no Visualizador de eventos.  Você pode encontrá-lo examinando a ID do evento "25" e verificar a data e a hora.

    ![ID do relatório de logs de envio de Gerenciamento de Dados gateway](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-report-id.png)    

### <a name="archive-gateway-logs-on-gateway-host-machine"></a>Arquivar logs de gateway no computador host do gateway
Há alguns cenários em que você tem problemas de gateway e não pode compartilhar logs de gateway diretamente:

* Você instala manualmente o gateway e registra o gateway.
* Você tenta registrar o gateway com uma chave regenerada no Configuration Manager Gerenciamento de Dados gateway.
* Você tenta enviar logs e o serviço de host do gateway não pode ser conectado.

Para esses cenários, você pode salvar os logs de gateway como um arquivo zip e compartilhá-los quando entrar em contato com o suporte da Microsoft. Por exemplo, se você receber um erro ao registrar o gateway, conforme mostrado na captura de tela a seguir.   

![Erro de registro do Gerenciamento de Dados gateway](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-registration-error.png)

Clique no link **arquivar logs de gateway** para arquivar e salvar logs e, em seguida, compartilhe o arquivo zip com o suporte da Microsoft.

![Gerenciamento de Dados logs de arquivo do gateway](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-archive-logs.png)

### <a name="locate-gateway-logs"></a>Localizar logs de gateway
Você pode encontrar informações detalhadas do log de gateway nos logs de eventos do Windows.

1. Inicie o Windows **Visualizador de eventos**.
2. Localize os logs nos **logs de aplicativos e serviços** > pasta **Gerenciamento de dados gateway** .

   Quando estiver solucionando problemas relacionados ao gateway, Procure eventos de nível de erro no Visualizador de eventos.

![Gerenciamento de Dados logs de gateway no Visualizador de eventos](media/data-factory-troubleshoot-gateway-issues/gateway-logs-event-viewer.png)
