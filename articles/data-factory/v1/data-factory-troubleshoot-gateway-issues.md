---
title: Problemas de resolução de dados Questões gateway de gestão de dados
description: Fornece dicas para resolver problemas relacionados com o Data Management Gateway.
author: nabhishek
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/01/2017
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 284486c5db248ced8ada6e7194c7bc5a9be5689f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100388350"
---
# <a name="troubleshoot-issues-with-using-data-management-gateway"></a>Utilizar o Data Management Gateway para resolver problemas
Este artigo fornece informações sobre problemas de resolução de problemas com a utilização do Data Management Gateway.

> [!NOTE]
> Este artigo aplica-se à versão 1 do Azure Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte o [tempo de integração auto-hospedado na Data Factory](../create-self-hosted-integration-runtime.md).

Consulte o artigo [do Data Management Gateway](data-factory-data-management-gateway.md) para obter informações detalhadas sobre o portal. Consulte os [dados do Move entre as instalações e](data-factory-move-data-between-onprem-and-cloud.md) o artigo em nuvem para obter uma passagem de dados de mudança de uma base de dados do SQL Server para o armazenamento do Microsoft Azure Blob utilizando o gateway.

## <a name="failed-to-install-or-register-gateway"></a>Falha na instalação ou registo do gateway
### <a name="1-problem"></a>1. Problema
Você vê esta mensagem de erro ao instalar e registar um gateway, especificamente, enquanto descarrega o ficheiro de instalação gateway.

`Unable to connect to the remote server". Please check your local settings (Error Code: 10003).`

#### <a name="cause"></a>Causa
A máquina em que está a tentar instalar o gateway não conseguiu descarregar o mais recente ficheiro de instalação gateway do centro de descarregamento devido a um problema de rede.

#### <a name="resolution"></a>Resolução
Verifique as definições do servidor de procuração de firewall para ver se as definições bloqueiam a ligação de rede do computador para o [centro de descarregamento](https://download.microsoft.com/)e atualiza as definições em conformidade.

Em alternativa, pode descarregar o ficheiro de instalação para o mais recente gateway a partir do centro de [descarregamento](https://www.microsoft.com/download/details.aspx?id=39717) de outras máquinas que possam aceder ao centro de descarregamento. Em seguida, pode copiar o ficheiro do instalador para o computador anfitrião gateway e executá-lo manualmente para instalar e atualizar o gateway.

### <a name="2-problem"></a>2. Problema
Vê este erro quando está a tentar instalar um gateway clicando **na instalação diretamente neste computador** no portal Azure.

`Error:  Abort installing a new gateway on this computer because this computer has an existing installed gateway and a computer without any installed gateway is required for installing a new gateway.`  

#### <a name="cause"></a>Causa
Já está instalado um portal na máquina.

#### <a name="resolution"></a>Resolução
Desinstale novamente o portal existente na máquina e clique na **instalação diretamente neste** link de computador.

### <a name="3-problem"></a>3. Problema
Pode ver este erro ao registar um novo portal.

`Error: The gateway has encountered an error during registration.`

#### <a name="cause"></a>Causa
Pode ver esta mensagem por uma das seguintes razões:

* O formato da tecla gateway é inválido.
* A chave do portal foi invalidada.
* A chave de entrada foi regenerada a partir do portal.  

#### <a name="resolution"></a>Resolução
Verifique se está a utilizar a tecla de porta de entrada certa a partir do portal. Se necessário, regenerar uma chave e utilizar a chave para registar o portal.

### <a name="4-problem"></a>4. Problema
Pode ver a seguinte mensagem de erro quando estiver a registar um portal.

`Error: The content or format of the gateway key "{gatewayKey}" is invalid, please go to azure portal to create one new gateway or regenerate the gateway key.`



![O conteúdo ou formato da chave é inválido](media/data-factory-troubleshoot-gateway-issues/invalid-format-gateway-key.png)

#### <a name="cause"></a>Causa
O conteúdo ou formato da tecla de porta de entrada está incorreto. Uma das razões pode ser que tenha copiado apenas uma parte da chave do portal ou esteja a usar uma chave inválida.

#### <a name="resolution"></a>Resolução
Gere uma tecla de gateway no portal e usa o botão de cópia para copiar toda a tecla. Em seguida, cole-o nesta janela para registar o portal.

### <a name="5-problem"></a>5. Problema
Pode ver a seguinte mensagem de erro quando estiver a registar um portal.

`Error: The gateway key is invalid or empty. Specify a valid gateway key from the portal.`

![Screenshot que realça a mensagem de erro que indica que a tecla gateway é inválida ou vazia.](media/data-factory-troubleshoot-gateway-issues/gateway-key-is-invalid-or-empty.png)

#### <a name="cause"></a>Causa
A chave de porta de entrada foi regenerada ou o portal foi apagado no portal Azure. Também pode acontecer se a configuração do Gateway de Gestão de Dados não for a mais recente.

#### <a name="resolution"></a>Resolução
Verifique se a configuração do Data Management Gateway é a versão mais recente, pode encontrar a versão mais recente no [centro de descarregamento](https://go.microsoft.com/fwlink/p/?LinkId=271260)da Microsoft.

Se a configuração for atual/ mais recente e o gateway ainda existir no Portal, regenerar a tecla de gateway no portal Azure e utilizar o botão de cópia para copiar toda a tecla e, em seguida, cole-a nesta janela para registar o gateway. Caso contrário, recrie o portal e comece de novo.

### <a name="6-problem"></a>6. Problema
Pode ver a seguinte mensagem de erro quando estiver a registar um portal.

`Error: Gateway has been online for a while, then shows "Gateway is not registered" with the status "Gateway key is invalid"`

![A chave gateway é inválida ou vazia](media/data-factory-troubleshoot-gateway-issues/gateway-not-registered-key-invalid.png)

#### <a name="cause"></a>Causa
Este erro pode acontecer porque ou o gateway foi eliminado ou a tecla de gateway associada foi regenerada.

#### <a name="resolution"></a>Resolução
Se o gateway tiver sido apagado, recriar o gateway a partir do portal, clique em **Registar,** copie a chave do portal, cole-a e tente registar o gateway.

Se o gateway ainda existir mas a sua chave foi regenerada, use a nova chave para registar o gateway. Se não tiver a chave, regenera a chave novamente do portal.

### <a name="7-problem"></a>7. Problema
Quando estiver a registar um portal, poderá ter de introduzir caminho e senha para obter um certificado.

![Screenshot que mostra onde você entra no caminho e senha para o certificado.](media/data-factory-troubleshoot-gateway-issues/specify-certificate.png)

#### <a name="cause"></a>Causa
O portal já foi registado noutras máquinas antes. Durante o registo inicial de um gateway, um certificado de encriptação foi associado com o gateway. O certificado pode ser autogerado pelo gateway ou fornecido pelo utilizador.  Este certificado é utilizado para encriptar credenciais da loja de dados (serviço ligado).  

![Exportar o certificado](media/data-factory-troubleshoot-gateway-issues/export-certificate.png)

Ao restaurar o gateway numa máquina hospedeira diferente, o assistente de registo pede que este certificado desencriptasse credenciais previamente encriptadas com este certificado.  Sem este certificado, as credenciais não podem ser desencriptadas pelo novo portal e as execuções subsequentes de atividades de cópia associadas a este novo portal falharão.  

#### <a name="resolution"></a>Resolução
Se tiver exportado o certificado de credencial da máquina de gateway original utilizando o botão **Exportação** no **separador Definições** no Gestor de Configurações do Gateway de Gestão de Dados, utilize o certificado aqui.

Não pode saltar esta fase quando se recupera um portal. Se faltar o certificado, tem de apagar o portal e recriar um novo portal.  Além disso, atualize todos os serviços ligados que estejam relacionados com a porta de entrada, reentrando nas suas credenciais.

### <a name="8-problem"></a>8. Problema
Pode ver a seguinte mensagem de erro.

`Error: The remote server returned an error: (407) Proxy Authentication Required.`

#### <a name="cause"></a>Causa
Este erro ocorre quando o seu gateway está num ambiente que requer um proxy HTTP para aceder aos recursos da Internet, ou a senha de autenticação do seu representante é alterada, mas não é atualizada em conformidade no seu gateway.

#### <a name="resolution"></a>Resolução
Siga as instruções na secção de considerações do servidor Proxy deste artigo e configufique as definições de procuração com o Gestor de Configuração gateway de gestão de dados.

## <a name="gateway-is-online-with-limited-functionality"></a>Gateway está on-line com funcionalidade limitada
### <a name="1-problem"></a>1. Problema
Você vê o estado do gateway como on-line com funcionalidade limitada.

#### <a name="cause"></a>Causa
Você vê o estado do gateway como on-line com funcionalidade limitada por uma das seguintes razões:

* Gateway não pode ligar-se ao serviço de nuvem através do Azure Service Bus.
* O serviço de nuvem não pode ligar-se ao gateway através do Service Bus.

Quando o gateway estiver online com uma funcionalidade limitada, poderá não ser capaz de utilizar o Assistente de Cópia de Data Factory para criar pipelines de dados para copiar dados para ou a partir de lojas de dados no local. Como solução alternativa, pode utilizar o Data Factory Editor no portal, Visual Studio ou Azure PowerShell.

#### <a name="resolution"></a>Resolução
A resolução para este problema (online com funcionalidade limitada) baseia-se na possibilidade de o gateway não se ligar ao serviço de nuvem ou ao contrário. As seguintes secções fornecem estas resoluções.

### <a name="2-problem"></a>2. Problema
Vê o seguinte erro.

`Error: Gateway cannot connect to cloud service through service bus`

![Gateway não pode ligar-se ao serviço de nuvem](media/data-factory-troubleshoot-gateway-issues/gateway-cannot-connect-to-cloud-service.png)

#### <a name="cause"></a>Causa
Gateway não pode ligar-se ao serviço de nuvem através do Service Bus.

#### <a name="resolution"></a>Resolução
Siga estes passos para voltar a estar online:

1. Permitir regras de saída de endereço IP na máquina de gateway e na firewall corporativa. Pode encontrar endereços IP a partir do Registo de Eventos do Windows (ID == 401): Foi feita uma tentativa de aceder a uma tomada de uma forma proibida pelas suas permissões de acesso XX.XX.XX.XX:9350.
1. Configurar configurações de procuração no gateway. Consulte a secção de considerações do servidor Proxy para obter mais detalhes.
1. Ative as portas de saída 5671 e 9350-9354 tanto no Windows Firewall na máquina de gateway como na firewall corporativa. Consulte a secção de portas e firewall para mais detalhes. Este passo é opcional, mas recomendamos para consideração de desempenho.

### <a name="3-problem"></a>3. Problema
Vê o seguinte erro.

`Error: Cloud service cannot connect to gateway through service bus.`

#### <a name="cause"></a>Causa
Um erro transitório na conectividade da rede.

#### <a name="resolution"></a>Resolução
Siga estes passos para voltar a estar online:

1. Aguarde alguns minutos, a conectividade será automaticamente recuperada quando o erro desaparecer.
1. Se o erro persistir, reinicie o serviço de gateway.

## <a name="failed-to-author-linked-service"></a>Falha no serviço ligado ao autor
### <a name="problem"></a>Problema
Poderá ver este erro quando tentar utilizar o Gestor credencial no portal para inserir credenciais para um novo serviço ligado ou atualizar credenciais para um serviço ligado existente.

`Error: The data store '<Server>/<Database>' cannot be reached. Check connection settings for the data source.`

Quando vir este erro, a página de definições do Gestor de Configuração gateway de gestão de dados pode parecer a seguinte imagem.

![A base de dados não pode ser alcançada](media/data-factory-troubleshoot-gateway-issues/database-cannot-be-reached.png)

#### <a name="cause"></a>Causa
O certificado TLS/SSL pode ter sido perdido na máquina de gateway. O computador gateway não pode carregar o certificado atualmente utilizado para encriptação TLS. Também pode ver uma mensagem de erro no registo de eventos que é semelhante à seguinte.

 `Unable to get the gateway settings from cloud service. Check the gateway key and the network connection. (Certificate with thumbprint cannot be loaded.)`

#### <a name="resolution"></a>Resolução
Siga estes passos para resolver o problema:

1. Iniciar gestor de configuração gateway de gestão de dados.
2. Mudar para o separador **Definições**.  
3. Clique no botão **Alterar** para alterar o certificado TLS/SSL.

   ![Alterar botão de certificado](media/data-factory-troubleshoot-gateway-issues/change-button-ssl-certificate.png)
4. Selecione um novo certificado como certificado TLS/SSL. Pode utilizar qualquer certificado TLS/SSL que seja gerado por si ou por qualquer organização.

   ![Especifique o certificado](media/data-factory-troubleshoot-gateway-issues/specify-http-end-point.png)

## <a name="copy-activity-fails"></a>A atividade de cópia falha
### <a name="problem"></a>Problema
Poderá notar a seguinte falha "UserErrorFailedToConnectToSqlserver" depois de ter configurado um pipeline no portal.

`Error: Copy activity encountered a user error: ErrorCode=UserErrorFailedToConnectToSqlServer,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Cannot connect to SQL Server`

#### <a name="cause"></a>Causa
Isto pode acontecer por diferentes razões, e a mitigação varia em conformidade.

#### <a name="resolution"></a>Resolução
Permitir ligações TCP de saída sobre a porta TCP/1433 no lado do cliente data Management Gateway antes de ligar a uma base de dados SQL.

Se a base de dados-alvo estiver na Base de Dados Azure SQL, verifique também as definições de firewall do SQL Server para Azure.

Consulte a seguinte secção para testar a ligação à loja de dados no local.

## <a name="data-store-connection-or-driver-related-errors"></a>Ligação de loja de dados ou erros relacionados com o condutor
Se vir a ligação à loja de dados ou erros relacionados com o condutor, complete os seguintes passos:

1. Iniciar gestor de configuração gateway de gestão de dados na máquina de gateway.
2. Mude para o **separador Diagnósticos.**
3. Em **Test Connection**, adicione os valores do grupo gateway.
4. Clique em **Testar** para ver se consegue ligar à fonte de dados no local a partir da máquina de gateway utilizando as informações e credenciais de ligação. Depois de instalar um controlador, se a ligação de teste continuar a falhar, reinicie o gateway para que este possa escolher a mais recente.

![Ligação de teste no separador diagnósticos](media/data-factory-troubleshoot-gateway-issues/test-connection-in-diagnostics-tab.png)

## <a name="gateway-logs"></a>Registos do gateway
### <a name="send-gateway-logs-to-microsoft"></a>Enviar registos de gateway para a Microsoft
Quando contactar o Microsoft Support para obter ajuda com problemas de resolução de problemas de gateway, poderá ser-lhe pedido que partilhe os seus registos de gateway. Com a libertação do gateway, pode partilhar os registos de gateways necessários com dois cliques de botão no Data Management Gateway Configuration Manager.    

1. Mude para o separador **Diagnósticos** no Gestor de Configuração gateway de gestão de dados.

    ![Separador de diagnósticos gateway de gestão de dados](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-diagnostics-tab.png)
2. Clique **em Enviar Registos** para ver a seguinte caixa de diálogo.

    ![Gateway de Gestão de Dados Enviar registos](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-dialog.png)
3. (Opcional) Clique **em ver registos** para rever registos no visualizador do evento.
4. (Opcional) Clique **em privacidade** para rever a declaração de privacidade dos serviços web da Microsoft.
5. Quando estiver satisfeito com o que está **prestes** a carregar, clique em Enviar Registos para enviar os registos dos últimos sete dias para a Microsoft para resolução de problemas. Deve ver o estado da operação de registos de envio, tal como mostrado na imagem seguinte.

    ![Screenshot que mostra onde ver o estado da operação de registos de envio.](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-status.png)
6. Depois de concluída a operação, vê uma caixa de diálogo como mostrado na imagem seguinte.

    ![Gateway de Gestão de Dados Enviar registos](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-result.png)
7. Guarde o **ID do relatório** e partilhe-o com o Microsoft Support. A identificação do relatório é usada para localizar os registos de gateway que carregou para a resolução de problemas.  O ID do relatório também é guardado no espectador do evento.  Você pode encontrá-lo olhando para o iD do evento "25", e verificar a data e hora.

    ![Gateway de Gestão de Dados Enviar registos reportar ID](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-report-id.png)    

### <a name="archive-gateway-logs-on-gateway-host-machine"></a>Registos de gateway de arquivo na máquina de anfitrião gateway
Existem alguns cenários em que tem problemas de gateway e não pode partilhar registos de gateway diretamente:

* Instale manualmente o portal e registe o portal.
* Tente registar o portal com uma chave regenerada no Gestor de Configuração gateway de gestão de dados.
* Tente enviar registos e o serviço de anfitrião gateway não pode ser ligado.

Para estes cenários, pode guardar registos de gateway como um ficheiro zip e partilhá-los quando contactar o suporte da Microsoft. Por exemplo, se receber um erro enquanto regista o gateway como mostrado na imagem seguinte.   

![Erro de registo gateway de gestão de dados](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-registration-error.png)

Clique no link de **registos de gateway Archive** para arquivar e guardar registos e, em seguida, partilhe o ficheiro zip com o suporte da Microsoft.

![Registos do Arquivo Gateway de Gestão de Dados](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-archive-logs.png)

### <a name="locate-gateway-logs"></a>Localizar registos de gateway
Pode encontrar informações detalhadas sobre registos de gateway nos registos de eventos do Windows.

1. Iniciar **o visualizador de eventos do Windows**.
2. Localizar registos na pasta Gateway de Gestão de Dados de **Registos de Aplicações e Serviços.**  >  

   Quando estiver a resolver problemas relacionados com gateways, procure eventos de nível de erro no espectador do evento.

![Data Management Gateway regista no espectador de eventos](media/data-factory-troubleshoot-gateway-issues/gateway-logs-event-viewer.png)
