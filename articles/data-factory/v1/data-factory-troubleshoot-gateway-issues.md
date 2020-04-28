---
title: Problemas problemas de gestão de dados Gateway
description: Fornece dicas para resolver problemas relacionados com gateway de gestão de dados.
services: data-factory
author: nabhishek
manager: anandsub
ms.assetid: c6756c37-4e5a-4d1e-ab52-365f149b4128
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/01/2017
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 09d51de3ae0bd4baca585d2abdd936b1a29567d0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80065034"
---
# <a name="troubleshoot-issues-with-using-data-management-gateway"></a>Utilizar o Data Management Gateway para resolver problemas
Este artigo fornece informações sobre problemas de resolução de problemas com a utilização do Gateway de Gestão de Dados.

> [!NOTE]
> Este artigo aplica-se à versão 1 do Azure Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte o tempo de execução da [integração auto-hospedado na Data Factory](../create-self-hosted-integration-runtime.md).

Consulte o artigo gateway de [gestão](data-factory-data-management-gateway.md) de dados para obter informações detalhadas sobre o gateway. Consulte os dados do Move entre as [instalações e](data-factory-move-data-between-onprem-and-cloud.md) o artigo em nuvem para uma passagem de dados em movimento de uma base de dados do SQL Server no local para o armazenamento do Microsoft Azure Blob utilizando o gateway.

## <a name="failed-to-install-or-register-gateway"></a>Falha na instalação ou registo de gateway
### <a name="1-problem"></a>1. Problema
Vê esta mensagem de erro ao instalar e registar um portal, especificamente, enquanto descarrega o ficheiro de instalação do gateway.

`Unable to connect to the remote server". Please check your local settings (Error Code: 10003).`

#### <a name="cause"></a>Causa
A máquina na qual está a tentar instalar o portal não conseguiu descarregar o mais recente ficheiro de instalação de gateway do centro de descarregamento devido a um problema de rede.

#### <a name="resolution"></a>Resolução
Verifique as definições do servidor proxy de firewall para ver se as definições bloqueiam a ligação de rede do computador para o centro de [descarregamento](https://download.microsoft.com/)e atualize as definições em conformidade.

Em alternativa, pode descarregar o ficheiro de instalação para o mais recente portal do centro de [descarregamento](https://www.microsoft.com/download/details.aspx?id=39717) de outras máquinas que podem aceder ao centro de descarregamento. Em seguida, pode copiar o ficheiro do instalador para o computador anfitrião do portal e executá-lo manualmente para instalar e atualizar o gateway.

### <a name="2-problem"></a>2. Problema
Vê este erro quando está a tentar instalar um portal clicando **em instalar diretamente neste computador** no portal Azure.

`Error:  Abort installing a new gateway on this computer because this computer has an existing installed gateway and a computer without any installed gateway is required for installing a new gateway.`  

#### <a name="cause"></a>Causa
Já está instalado um portal na máquina.

#### <a name="resolution"></a>Resolução
Desinstale o gateway existente na máquina e clique na **instalação diretamente neste link do computador novamente.**

### <a name="3-problem"></a>3. Problema
Pode ver este erro ao registar uma nova porta de entrada.

`Error: The gateway has encountered an error during registration.`

#### <a name="cause"></a>Causa
Pode ver esta mensagem por uma das seguintes razões:

* O formato da tecla gateway é inválido.
* A chave do portal foi invalidada.
* A chave do portal foi regenerada a partir do portal.  

#### <a name="resolution"></a>Resolução
Verifique se está a utilizar a tecla de gateway certa a partir do portal. Se necessário, regenerar uma chave e utilizar a chave para registar o portal.

### <a name="4-problem"></a>4. Problema
Pode ver a seguinte mensagem de erro quando estiver a registar uma porta de entrada.

`Error: The content or format of the gateway key "{gatewayKey}" is invalid, please go to azure portal to create one new gateway or regenerate the gateway key.`



![Conteúdo ou formato de chave é inválido](media/data-factory-troubleshoot-gateway-issues/invalid-format-gateway-key.png)

#### <a name="cause"></a>Causa
O conteúdo ou formato da tecla gateway de entrada está incorreto. Uma das razões pode ser que copiou apenas uma parte da chave do portal ou está a usar uma chave inválida.

#### <a name="resolution"></a>Resolução
Gere uma chave de gateway no portal e use o botão de cópia para copiar toda a tecla. Em seguida, colá-lo nesta janela para registar o portal.

### <a name="5-problem"></a>5. Problema
Pode ver a seguinte mensagem de erro quando estiver a registar uma porta de entrada.

`Error: The gateway key is invalid or empty. Specify a valid gateway key from the portal.`

![A chave gateway é inválida ou vazia](media/data-factory-troubleshoot-gateway-issues/gateway-key-is-invalid-or-empty.png)

#### <a name="cause"></a>Causa
A chave de gateway foi regenerada ou a porta de entrada foi eliminada no portal Azure. Também pode acontecer se a configuração do Portal de Gestão de Dados não for mais recente.

#### <a name="resolution"></a>Resolução
Verifique se a configuração do Gateway de Gestão de Dados é a versão mais recente, pode encontrar a versão mais recente no [centro de descarregamento](https://go.microsoft.com/fwlink/p/?LinkId=271260)da Microsoft .

Se a configuração for atual/ mais recente e o portal ainda existir no Portal, regenerar a chave de gateway no portal Azure e utilizar o botão de cópia para copiar toda a tecla e, em seguida, colá-la nesta janela para registar o gateway. Caso contrário, recrie o portal e recomece.

### <a name="6-problem"></a>6. Problema
Pode ver a seguinte mensagem de erro quando estiver a registar uma porta de entrada.

`Error: Gateway has been online for a while, then shows “Gateway is not registered” with the status “Gateway key is invalid”`

![A chave gateway é inválida ou vazia](media/data-factory-troubleshoot-gateway-issues/gateway-not-registered-key-invalid.png)

#### <a name="cause"></a>Causa
Este erro pode acontecer porque ou o portal foi apagado ou a chave de gateway associada foi regenerada.

#### <a name="resolution"></a>Resolução
Se o portal tiver sido apagado, recriar o portal, clicar **no Register,** copiar a chave do portal, colar e tentar registar o portal.

Se o portal ainda existir, mas a sua chave foi regenerada, utilize a nova chave para registar o portal. Se não tiver a chave, regenera a chave novamente do portal.

### <a name="7-problem"></a>7. Problema
Ao registar uma porta de entrada, poderá ter de introduzir caminho e senha para um certificado.

![Especificar certificado](media/data-factory-troubleshoot-gateway-issues/specify-certificate.png)

#### <a name="cause"></a>Causa
O portal já foi registado noutras máquinas antes. Durante o registo inicial de um portal, um certificado de encriptação foi associado ao portal. O certificado pode ser autogerado pelo portal ou fornecido pelo utilizador.  Este certificado é utilizado para encriptar credenciais da loja de dados (serviço ligado).  

![Exportar o certificado](media/data-factory-troubleshoot-gateway-issues/export-certificate.png)

Ao restaurar a porta de entrada numa máquina hospedeira diferente, o assistente de registo pede que este certificado desencriptasse as credenciais anteriormente encriptadas com este certificado.  Sem este certificado, as credenciais não podem ser desencriptadas pela nova porta de entrada e as execuções subsequentes de atividade de cópia associadas a este novo gateway falharão.  

#### <a name="resolution"></a>Resolução
Se tiver exportado o certificado de credencial da máquina original de gateway utilizando o botão **Export no** separador **Definições** no Gestor de Configuração gateway de gestão de dados, utilize o certificado aqui.

Não pode saltar esta fase quando se recupera um portal. Se o certificado estiver em falta, tem de apagar o portal e recriar um novo portal.  Além disso, atualize todos os serviços ligados que estejam relacionados com o portal, reentrando nas suas credenciais.

### <a name="8-problem"></a>8. Problema
Pode ver a seguinte mensagem de erro.

`Error: The remote server returned an error: (407) Proxy Authentication Required.`

#### <a name="cause"></a>Causa
Este erro ocorre quando o seu portal está num ambiente que requer um proxy HTTP para aceder aos recursos da Internet, ou a palavra-passe de autenticação do seu representante é alterada, mas não é atualizada em conformidade no seu portal.

#### <a name="resolution"></a>Resolução
Siga as instruções na secção de considerações do servidor Proxy deste artigo e configure as definições de procuração com o Gestor de Configuração de Gateway de Gestão de Dados.

## <a name="gateway-is-online-with-limited-functionality"></a>Gateway está online com funcionalidade limitada
### <a name="1-problem"></a>1. Problema
Vê o estado do portal como online com funcionalidade limitada.

#### <a name="cause"></a>Causa
Vê o estado do portal como online com funcionalidade limitada por uma das seguintes razões:

* Gateway não pode ligar ao serviço de nuvem através do Azure Service Bus.
* O serviço de nuvem não pode ligar-se ao gateway através do Service Bus.

Quando o gateway está on-line com funcionalidade limitada, pode não ser capaz de utilizar o Data Factory Copy Wizard para criar pipelines de dados para copiar dados de ou para lojas de dados no local. Como suticidade, pode utilizar o Data Factory Editor no portal, Visual Studio ou Azure PowerShell.

#### <a name="resolution"></a>Resolução
A resolução para este problema (online com funcionalidade limitada) baseia-se em se o gateway não pode ligar-se ao serviço de nuvem ou ao contrário. As seguintes secções fornecem estas resoluções.

### <a name="2-problem"></a>2. Problema
Vê o seguinte erro.

`Error: Gateway cannot connect to cloud service through service bus`

![Gateway não pode ligar ao serviço de nuvem](media/data-factory-troubleshoot-gateway-issues/gateway-cannot-connect-to-cloud-service.png)

#### <a name="cause"></a>Causa
Gateway não pode ligar-se ao serviço de nuvem através do Service Bus.

#### <a name="resolution"></a>Resolução
Siga estes passos para que o portal volte a funcionar:

1. Permita regras de saída de endereçoIP na máquina de gateway e na firewall corporativa. Pode encontrar endereços IP do Windows Event Log (ID == 401): Foi feita uma tentativa de aceder a uma tomada de uma forma proibida pelas suas permissões de acesso XX. XX. XX. XX:9350.
1. Configure as definições de procuração no portal. Consulte a secção de considerações do servidor Proxy para obter mais detalhes.
1. Ative as portas de saída 5671 e 9350-9354 tanto no Windows Firewall na máquina gateway como na firewall corporativa. Consulte a secção Portas e firewall para mais detalhes. Este passo é opcional, mas recomendamo-lo para consideração de desempenho.

### <a name="3-problem"></a>3. Problema
Vê o seguinte erro.

`Error: Cloud service cannot connect to gateway through service bus.`

#### <a name="cause"></a>Causa
Um erro transitório na conectividade da rede.

#### <a name="resolution"></a>Resolução
Siga estes passos para que o portal volte a funcionar:

1. Aguarde alguns minutos, a conectividade será automaticamente recuperada quando o erro desaparecer.
1. Se o erro persistir, reinicie o serviço de gateway.

## <a name="failed-to-author-linked-service"></a>Falha no serviço ligado ao autor
### <a name="problem"></a>Problema
Pode ver este erro quando tenta utilizar o Credential Manager no portal para inserir credenciais para um novo serviço ligado, ou atualizar credenciais para um serviço ligado existente.

`Error: The data store '<Server>/<Database>' cannot be reached. Check connection settings for the data source.`

Quando vir este erro, a página de definições do Gestor de Configuração gateway de gestão de dados pode parecer a seguinte imagem.

![A base de dados não pode ser alcançada](media/data-factory-troubleshoot-gateway-issues/database-cannot-be-reached.png)

#### <a name="cause"></a>Causa
O certificado TLS/SSL pode ter sido perdido na máquina de porta de entrada. O computador gateway não pode carregar o certificado atualmente que é usado para encriptação TLS. Pode também ver uma mensagem de erro no registo do evento semelhante à seguinte mensagem.

 `Unable to get the gateway settings from cloud service. Check the gateway key and the network connection. (Certificate with thumbprint cannot be loaded.)`

#### <a name="resolution"></a>Resolução
Siga estes passos para resolver o problema:

1. Inicie o Gestor de Configuração de Gateway de Gestão de Dados.
2. Mudar para o separador **Definições**.  
3. Clique no botão **Alterar** para alterar o certificado TLS/SSL.

   ![Alterar botão de certificado](media/data-factory-troubleshoot-gateway-issues/change-button-ssl-certificate.png)
4. Selecione um novo certificado como certificado TLS/SSL. Pode utilizar qualquer certificado TLS/SSL que seja gerado por si ou por qualquer organização.

   ![Especificar certificado](media/data-factory-troubleshoot-gateway-issues/specify-http-end-point.png)

## <a name="copy-activity-fails"></a>Falha a atividade de cópia
### <a name="problem"></a>Problema
Pode notar a seguinte falha "UserErrorFailedToConnectToSqlserver" depois de ter configurado um pipeline no portal.

`Error: Copy activity encountered a user error: ErrorCode=UserErrorFailedToConnectToSqlServer,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Cannot connect to SQL Server`

#### <a name="cause"></a>Causa
Isto pode acontecer por diferentes razões, e a mitigação varia em conformidade.

#### <a name="resolution"></a>Resolução
Permitir ligações TCP de saída sobre a porta TCP/1433 no lado do cliente gateway de gestão de dados antes de se ligar a uma base de dados SQL.

Se a base de dados do alvo for uma base de dados Azure SQL, verifique também as definições de firewall do SQL Server para o Azure.

Consulte a secção seguinte para testar a ligação ao armazém de dados no local.

## <a name="data-store-connection-or-driver-related-errors"></a>Ligação à loja de dados ou erros relacionados com o condutor
Se vir a ligação da loja de dados ou erros relacionados com o condutor, complete os seguintes passos:

1. Inicie o Gestor de Configuração de Gateway de Gestão de Dados na máquina gateway.
2. Mude para o separador **Diagnósticos.**
3. Em **Ligação de Teste,** adicione os valores do grupo gateway.
4. Clique em **Testar** para ver se consegue ligar-se à fonte de dados no local a partir da máquina de gateway utilizando as informações e credenciais de ligação. Depois de instalar um controlador, se a ligação de teste continuar a falhar, reinicie o gateway para que este possa escolher a mais recente.

![Ligação de teste no separador de diagnóstico](media/data-factory-troubleshoot-gateway-issues/test-connection-in-diagnostics-tab.png)

## <a name="gateway-logs"></a>Registos do gateway
### <a name="send-gateway-logs-to-microsoft"></a>Enviar registos de gateway para a Microsoft
Quando contactar o Microsoft Support para obter ajuda com problemas de resolução de problemas, poderá ser-lhe pedido que partilhe os seus registos de gateway. Com o lançamento do gateway, pode partilhar os registos de gateway necessários com dois cliques de botão no Gestor de Configuração de Gateway de Gestão de Dados.    

1. Mude para o separador **Diagnósticos** no Gestor de Configuração gateway de gestão de dados.

    ![Separador de diagnóstico gateway de gestão de dados](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-diagnostics-tab.png)
2. Clique em **Enviar Registos** para ver a seguinte caixa de diálogo.

    ![Gateway de Gestão de Dados Enviar registos](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-dialog.png)
3. (Opcional) Clique em **registos de visualização** para rever os registos no espectador do evento.
4. (Opcional) Clique em **privacidade** para rever a declaração de privacidade dos serviços web da Microsoft.
5. Quando estiver satisfeito com o que está prestes a carregar, clique em **Enviar Registos** para enviar os registos dos últimos sete dias para a Microsoft para resolução de problemas. Deve ver o estado da operação de registos de envio, tal como mostrado na seguinte imagem.

    ![Gateway de Gestão de Dados Enviar o estado dos registos](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-status.png)
6. Após a operação estar concluída, vê uma caixa de diálogo como mostrado na seguinte imagem.

    ![Gateway de Gestão de Dados Enviar o estado dos registos](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-result.png)
7. Guarde o ID do **Relatório** e partilhe-o com o Suporte da Microsoft. O ID do relatório é usado para localizar os registos de gateway que carregou para resolução de problemas.  O ID do relatório também é guardado no espectador do evento.  Você pode encontrá-lo olhando para o ID do evento "25", e verificar a data e hora.

    ![Gateway de Gestão de Dados Enviar registos reportar ID](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-report-id.png)    

### <a name="archive-gateway-logs-on-gateway-host-machine"></a>Registos de gateway de arquivo na máquina anfitriã de gateway
Existem alguns cenários em que tem problemas com gateways e não pode partilhar registos de gateway diretamente:

* Instale manualmente o portal e registe o portal.
* Tenta registar o portal com uma chave regenerada no Gestor de Configuração gateway de gestão de dados.
* Tente enviar registos e o serviço de hospedaria de gateway não pode ser ligado.

Para estes cenários, pode guardar os registos de gateway como ficheiro zip e partilhá-lo quando entrar em contacto com o suporte da Microsoft. Por exemplo, se receber um erro enquanto regista o gateway, como mostrado na seguinte imagem.   

![Erro de registo de gateway de gestão de dados](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-registration-error.png)

Clique no link de **registos** de gateway do Arquivo para arquivar e guardar registos e, em seguida, partilhe o ficheiro zip com o suporte da Microsoft.

![Registos de Arquivo gateway de gestão de dados](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-archive-logs.png)

### <a name="locate-gateway-logs"></a>Localizar registos de gateway
Pode encontrar informações detalhadas sobre o registo de gateway nos registos do evento windows.

1. Inicie **o Visualizador de Eventos**windows .
2. Localize os registos na pasta Gateway de**Gestão** de Dados de Gestão de Dados da **Aplicação e Serviços.** > 

   Quando estiver a resolver problemas relacionados com gateways, procure eventos de nível de erro no espectador do evento.

![Logs gateway de gestão de dados no espectador de eventos](media/data-factory-troubleshoot-gateway-issues/gateway-logs-event-viewer.png)
