---
title: Gateway de gestão de dados do Data Factory | Documentos da Microsoft
description: Configure um gateway de dados para mover dados entre locais e na cloud. Utilize o Data Management Gateway no Azure Data Factory para mover seus dados.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.assetid: b9084537-2e1c-4e96-b5bc-0e2044388ffd
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 28796c753e32a3c3b18052190618152c7669c93d
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57437076"
---
# <a name="data-management-gateway"></a>Data Management Gateway
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, veja [Self-hosted integration runtime no](../create-self-hosted-integration-runtime.md).

> [!NOTE]
> O Data Management Gateway agora tem sido marca alterada para como Integration Runtime autoalojado.

O Data management gateway é um agente de cliente que tem de instalar no seu ambiente no local para copiar dados entre arquivos de dados na cloud e no local. Os dados no local arquivos de fábrica de dados estão listados na [origens de dados suportadas](data-factory-data-movement-activities.md#supported-data-stores-and-formats) secção.

Este artigo complementa as instruções a [mover dados entre locais e na cloud arquivos de dados](data-factory-move-data-between-onprem-and-cloud.md) artigo. No passo a passo, vai criar um pipeline que utiliza o gateway para mover dados de uma base de dados do SQL Server no local para um blob do Azure. Este artigo fornece informações aprofundadas detalhadas sobre o data management gateway.

Pode aumentar horizontalmente um gateway de gestão de dados através da associação de várias máquinas no local com o gateway. Pode dimensionar até ao aumentar o número de tarefas de movimento de dados que podem ser executadas simultaneamente num nó. Esse recurso também está disponível para um gateway lógico com um único nó. Ver [gateway de gestão de dados de dimensionamento no Azure Data Factory](data-factory-data-management-gateway-high-availability-scalability.md) artigo para obter detalhes.

> [!NOTE]
> Atualmente, o gateway suporta apenas a atividade de cópia e a atividade de procedimento armazenado no Data Factory. Não é possível utilizar o gateway a partir de uma atividade personalizada para aceder a origens de dados no local.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Descrição geral
### <a name="capabilities-of-data-management-gateway"></a>Capacidades de gestão do gateway de dados
Gateway de gestão de dados fornece as seguintes capacidades:

* Origens de dados no local de modelos e origens de dados na mesma fábrica de dados na cloud e mover dados.
* Ter um único painel de vidro, para monitorização e gestão a visibilidade do Estado do gateway da página da fábrica de dados.
* Gerir o acesso às origens de dados no local com segurança.
  * Não existem alterações necessárias para firewall Corporativo. Gateway só faz baseado em HTTP as ligações de saída para a internet aberta.
  * Encripte as credenciais para os arquivos de dados no local com o certificado.
* Mover dados com eficiência – os dados são transferidos em paralelo, lógica de repetição de problemas de rede resiliente a intermitente com automática.

### <a name="command-flow-and-data-flow"></a>Fluxo de comando e fluxo de dados
Quando utiliza uma atividade de cópia para copiar dados entre aplicações no local e na cloud, a atividade utiliza um gateway para transferir dados de origem de dados no local para a cloud e vice-versa.

Eis o fluxo de dados de alto nível para e resumo das etapas de cópia com o gateway de dados: ![Fluxo de dados com o gateway](./media/data-factory-data-management-gateway/data-flow-using-gateway.png)

1. Programadores de dados cria um gateway para uma fábrica de dados do Azure através da [portal do Azure](https://portal.azure.com) ou [Cmdlet do PowerShell](https://docs.microsoft.com/powershell/module/az.datafactory/).
2. Programadores de dados cria um serviço ligado para um arquivo de dados no local, especificando o gateway. Como parte da configuração de serviço ligado, o desenvolvedor de dados utiliza a aplicação de credenciais de definição para especificar os tipos de autenticação e credenciais. A caixa de diálogo do aplicativo de definição de credenciais se comunica com o arquivo de dados para testar a ligação e o gateway para guardar as credenciais.
3. Gateway encripta as credenciais com o certificado associado ao gateway (fornecido pelo desenvolvedor de dados), antes de guardar as credenciais na nuvem.
4. Serviço do Data Factory se comunica com o gateway para agendamento e gestão de tarefas por meio de um canal de controlo que utiliza uma fila de barramento de serviço do Azure partilhado. Quando uma tarefa de atividade de cópia tem de arrancar, Data Factory coloca em fila o pedido, juntamente com informações de credenciais. Gateway inicia a tarefa depois da fila de consulta.
5. O gateway desencripta as credenciais com o mesmo certificado e, em seguida, liga-se ao armazenamento de dados no local com o tipo de autenticação adequada e as credenciais.
6. O gateway copia dados de um arquivo no local para um armazenamento na cloud, ou vice versa dependendo da configuração a atividade de cópia no pipeline de dados. Para este passo, o gateway comunica diretamente com os serviços de armazenamento baseado na nuvem, como o armazenamento de Blobs do Azure através de um canal de seguro (HTTPS).

### <a name="considerations-for-using-gateway"></a>Considerações sobre como utilizar o gateway
* Uma única instância de gateway de gestão de dados pode ser utilizada para várias origens de dados no local. No entanto, **uma única instância de gateway está ligada à fábrica de dados do Azure apenas um** e não pode ser partilhado com outro data factory.
* Pode ter **apenas uma instância de gateway de gestão de dados** instalado numa única máquina. Suponha, tem duas fábricas de dados que precisam de aceder a origens de dados no local, tem de instalar os gateways em dois computadores no local. Em outras palavras, um gateway está ligado a uma fábrica de dados específico
* O **gateway não tem de estar no mesmo computador, como a origem de dados**. No entanto, ter mais perto gateway para a origem de dados reduz o tempo para o gateway ligar à origem de dados. Recomendamos que instale o gateway num computador que é diferente da que é que aloja a origem de dados no local. Quando a origem de dados e de gateway em máquinas diferentes, o gateway não compitam por recursos com a origem de dados.
* Pode ter **vários gateways em computadores diferentes, ligar à mesma origem de dados no local**. Por exemplo, pode ter dois gateways que serve a duas fábricas de dados, mas a mesma origem de dados no local é registrada em ambas as fábricas de dados.
* Se já tiver um gateway instalado no seu serviço de computador uma **Power BI** cenário, instale um **gateway separada do Azure Data Factory** em outra máquina.
* Gateway tem de ser utilizado, mesmo quando usar **ExpressRoute**.
* Tratar a sua origem de dados como uma origem de dados no local (que é protegido por uma firewall), mesmo quando usar **ExpressRoute**. Utilize o gateway para estabelecer a conectividade entre o serviço e a origem de dados.
* Deve **utilizar o gateway** mesmo que o arquivo de dados seja na cloud num **VM IaaS do Azure**.

## <a name="installation"></a>Instalação
### <a name="prerequisites"></a>Pré-requisitos
* O suporte **sistema operativo** versões são o Windows 7, Windows 8/8.1, Windows 10, Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2. Instalação do data management gateway num controlador de domínio não é atualmente suportada.
* .NET framework 4.5.1 ou posterior é necessária. Se estiver a instalar o gateway num computador Windows 7, instale o .NET Framework 4.5 ou posterior. Ver [requisitos de sistema do .NET Framework](https://msdn.microsoft.com/library/8z6watww.aspx) para obter detalhes.
* O recomendado **configuração** para o gateway máquina é, pelo menos, 2 GHz, 4 núcleos, 8 GB de RAM e disco de 80 GB.
* Se a máquina de anfitrião hiberna, o gateway não responde a pedidos de dados. Por conseguinte, configurar apropriadas **esquema de energia** no computador antes de instalar o gateway. Se a máquina está configurada em hibernação, a instalação do gateway pede-lhe uma mensagem.
* Tem de ser um administrador no computador para instalar e configurar o data management gateway com êxito. Pode adicionar utilizadores adicionais para o **gateway de gestão de dados utilizadores** grupo local do Windows. Os membros deste grupo são capazes de utilizar o **Gestor de configuração do Data Management Gateway** ferramenta para configurar o gateway.

À medida que ocorrem execuções de atividade de cópia numa frequência específica, a utilização de recursos (CPU, memória) na máquina também segue o mesmo padrão com horas de pico e tempos de inatividade. Utilização de recursos também depende muito a quantidade de dados a ser movidos. Quando várias tarefas de cópia em curso, verá subir durante as horas de pico de utilização de recursos.

### <a name="installation-options"></a>Opções de instalação
O Data management gateway pode ser instalado das seguintes formas:

* Baixando um pacote de instalação do MSI do [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=39717). O MSI também pode ser utilizado para atualizar o gateway de gestão de dados existente para a versão mais recente, com todas as definições preservadas.
* Ao clicar em **transferir e instalar o gateway de dados** ligação em configuração MANUAL ou **instalar diretamente neste computador** em Configuração rápida. Ver [mover dados entre locais e na cloud](data-factory-move-data-between-onprem-and-cloud.md) artigo para obter instruções passo a passo sobre como utilizar a configuração rápida. O passo manual leva-o para o Centro de transferências. As instruções para transferir e instalar o gateway do Centro de transferências estão na secção seguinte.

### <a name="installation-best-practices"></a>Melhores práticas de instalação:
1. Configure o esquema de energia no computador anfitrião para o gateway para que a máquina sem a hibernação. Se a máquina de anfitrião hiberna, o gateway não responde a pedidos de dados.
2. Criar cópias de segurança o certificado associado ao gateway.

### <a name="install-the-gateway-from-download-center"></a>Instalar o gateway a partir do Centro de download
1. Navegue para [página de download da Microsoft Data Management Gateway](https://www.microsoft.com/download/details.aspx?id=39717).
2. Clique em **baixe**, selecione a versão adequada (**32-bit** vs. **64-bit**) e clique em **próxima**.
3. Executar o **MSI** diretamente ou guardá-lo para o seu disco rígido e a execução.
4. Sobre o **bem-vindo** página, selecione um **linguagem** clique em **seguinte**.
5. **Aceite** o contrato de licença de utilizador final e clique em **próxima**.
6. Selecione **pasta** para instalar o gateway e clique em **próxima**.
7. Sobre o **pronto para instalar o** página, clique em **instalar**.
8. Clique em **concluir** para concluir a instalação.
9. Obter a chave do portal do Azure. Veja a secção seguinte para obter instruções passo a passo.
10. Na **Registre-se gateway** página do **Gestor de configuração do Data Management Gateway** em execução no seu computador, siga os passos abaixo:
    1. Cole a chave no texto.
    2. Opcionalmente, clique em **chave de gateway Show** para ver o texto da chave.
    3. Clique em **registar**.

### <a name="register-gateway-using-key"></a>Registar com chave de gateway
#### <a name="if-you-havent-already-created-a-logical-gateway-in-the-portal"></a>Se ainda não tiver criado um gateway lógico no portal
Para criar um gateway no portal e obter a chave a partir da **configurar** página, siga passos do passo a passo no [mover dados entre locais e na cloud](data-factory-move-data-between-onprem-and-cloud.md) artigo.

#### <a name="if-you-have-already-created-the-logical-gateway-in-the-portal"></a>Se já tiver criado o gateway lógico no portal
1. No portal do Azure, navegue para o **Data Factory** página e clique em **serviços ligados** mosaico.

    ![Página da fábrica de dados](media/data-factory-data-management-gateway/data-factory-blade.png)
2. Na **serviços ligados** , selecione a lógica **gateway** que criou no portal.

    ![gateway lógico](media/data-factory-data-management-gateway/data-factory-select-gateway.png)
3. Na **Gateway de dados** página, clique em **transferir e instalar o gateway de dados**.

    ![Ligação no portal de transferência](media/data-factory-data-management-gateway/download-and-install-link-on-portal.png)
4. Na **configurar** página, clique em **recrie chave**. Clique em Sim na mensagem de aviso depois de lê-lo com cuidado.

    ![Recrie a chave](media/data-factory-data-management-gateway/recreate-key-button.png)
5. Clique em botão Copiar, junto a chave. A chave é copiada para a área de transferência.

    ![Chave de cópia](media/data-factory-data-management-gateway/copy-gateway-key.png)

### <a name="system-tray-icons-notifications"></a>Ícones de tabuleiro de sistema / notificações
A imagem seguinte mostra alguns ícones Bandeja que vê.

![ícones de tabuleiro de sistema](./media/data-factory-data-management-gateway/gateway-tray-icons.png)

Se mover o cursor sobre a mensagem de ícone/notificação do tabuleiro de sistema, verá detalhes sobre o estado da operação de atualização do gateway numa janela de pop-up.

### <a name="ports-and-firewall"></a>Portas e firewall
Existem duas firewalls, precisa considerar: **firewall Corporativo** em execução no router central da organização, e **firewall do Windows** configurado como um daemon no computador local em que é o gateway instalado.

![firewalls](./media/data-factory-data-management-gateway/firewalls2.png)

No nível de firewall da empresa, tem de configurar os seguintes domínios e as portas de saída:

| Nomes de domínio | Portas | Descrição |
| --- | --- | --- |
| *.servicebus.windows.net |443 |Utilizado para comunicação com o back-end do serviço de movimento de dados |
| *.core.windows.net |443 |Utilizado para a cópia de teste usando BLOBs do Azure (se configurada)|
| *.frontend.clouddatahub.net |443 |Utilizado para comunicação com o back-end do serviço de movimento de dados |
| *.servicebus.windows.net |9350-9354, 5671 |Reencaminhamento de barramento de serviço opcional sobre TCP utilizada pelo Assistente de cópia |

No nível de firewall do Windows, estas portas de saída normalmente estão ativadas. Se não, pode configurar as portas e domínios em conformidade no computador gateway.

> [!NOTE]
> 1. Com base na sua origem / sinks, poderá ter de domínios adicionais da lista de permissões e portas de saída na firewall empresarial/Windows.
> 2. Para algumas bases de dados na Cloud (por exemplo: [Base de dados do Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-configure-firewall-settings), [do Azure Data Lake](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-secure-data#set-ip-address-range-for-data-access), etc.), poderá ter de endereço IP da lista de permissões da máquina de Gateway em suas configurações de firewall.
>
>

#### <a name="copy-data-from-a-source-data-store-to-a-sink-data-store"></a>Copiar dados de um arquivo de dados de origem para um arquivo de dados de sink
Certifique-se de que as regras de firewall estão ativadas corretamente do firewall Corporativo, o firewall do Windows no computador gateway, e o arquivo de dados em si. Ativar estas regras, permite que o gateway ligar a ambas as origem e sink com êxito. Ative as regras para cada arquivo de dados que está envolvido na operação de cópia.

Por exemplo, para copiar a partir **um arquivo de dados no local para um sink de base de dados do Azure SQL ou um coletor do Azure SQL Data Warehouse**, siga os passos abaixo:

* Permitir saída **TCP** comunicações na porta **1433** para o firewall do Windows e o firewall Corporativo.
* Configure definições de firewall do servidor SQL do Azure para adicionar o endereço IP do computador gateway à lista de endereços IP permitidos.

> [!NOTE]
> Se a firewall não permite a saída através da porta 1433, Gateway não consegue aceder SQL do Azure diretamente. Neste caso, pode usar [cópia faseada](https://docs.microsoft.com/azure/data-factory/data-factory-copy-activity-performance#staged-copy) à base de dados do SQL Azure / armazém de dados do SQL Azure. Neste cenário, apenas exigiria HTTPS (porta 443) para o movimento de dados.
>
>

### <a name="proxy-server-considerations"></a>Considerações do servidor proxy
Se o seu ambiente de rede Corporativo utiliza um servidor proxy para aceder à internet, configure o data management gateway para utilizar definições de proxy apropriados. Pode definir o proxy durante a fase de registro inicial.

![Proxy de conjunto durante o registo](media/data-factory-data-management-gateway/SetProxyDuringRegistration.png)

Gateway utiliza o servidor proxy para ligar ao serviço cloud. Clique em **alteração** ligação durante a configuração inicial. Verá o **definição de proxy** caixa de diálogo.

![Utilizando o Gestor de configuração de proxy de conjunto](media/data-factory-data-management-gateway/SetProxySettings.png)

Existem três opções de configuração:

* **Não utilizar o proxy**: Gateway não utiliza explicitamente qualquer proxy para ligar a serviços cloud.
* **Utilizar o proxy do sistema**: O gateway utiliza a definição de proxy que está configurado no diahost.exe.config e diawp.exe.config. Se nenhum proxy estiver configurado no diahost.exe.config e diawp.exe.config, o gateway liga ao serviço em nuvem diretamente sem passar pelo proxy.
* **Utilizar proxy personalizado**: Configure a definição a utilizar para o gateway, em vez de usar configurações no diahost.exe.config e diawp.exe.config de proxy HTTP. O endereço e porta são necessários. Nome de utilizador e palavra-passe são opcionais, consoante a definição de autenticação de seu proxy. Todas as definições são encriptadas com o certificado da credencial do gateway e armazenadas localmente no computador do anfitrião de gateway.

O serviço de anfitrião do data management gateway é reiniciado automaticamente depois de guardar as definições de proxy atualizadas.

Depois de gateway ter sido registado com êxito, se pretender ver ou atualizar as definições de proxy, utilize o Gestor de configuração do Data Management Gateway.

1. Inicie **Gestor de configuração do Data Management Gateway**.
2. Mudar para o separador **Definições**.
3. Clique em **alteração** ligação na **HTTP Proxy** secção para iniciar o **definir o Proxy de HTTP** caixa de diálogo.
4. Depois de clicar no **seguinte** botão, verá uma caixa de diálogo de aviso solicitando a permissão Guardar a definição de proxy e reinicie o serviço de anfitrião de Gateway.

Pode ver e atualizar o proxy HTTP utilizando a ferramenta do Configuration Manager.

![Utilizando o Gestor de configuração de proxy de conjunto](media/data-factory-data-management-gateway/SetProxyConfigManager.png)

> [!NOTE]
> Se configurar um servidor proxy com a autenticação NTLM, o serviço de anfitrião de Gateway é executado sob a conta de domínio. Se alterar a palavra-passe para a conta de domínio mais tarde, lembre-se atualizar as definições de configuração para o serviço e reinicie-o em conformidade. Devido a este requisito, sugerimos que usar uma conta de domínio dedicado para aceder ao servidor de proxy que não necessita de atualizar a palavra-passe com frequência.
>
>

### <a name="configure-proxy-server-settings"></a>Configurar definições do servidor proxy
Se selecionou **utilizar o proxy do sistema** definir para o proxy HTTP, o gateway utiliza a definição no diahost.exe.config e diawp.exe.config de proxy. Se não for especificado nenhum proxy no diahost.exe.config e diawp.exe.config, o gateway liga ao serviço em nuvem diretamente sem passar pelo proxy. O procedimento seguinte fornece instruções para atualizar o ficheiro de diahost.exe.config.

1. No Explorador de ficheiros, faça uma cópia de segurança do C:\Program Files\Microsoft Data Management Gateway\2.0\Shared\diahost.exe.config para fazer backup do arquivo original.
2. Inicie Notepad.exe em execução como administrador e abrir o arquivo de texto "C:\Program Files\Microsoft Data Management Gateway\2.0\Shared\diahost.exe.config. Encontrará a etiqueta predefinida para system.net, conforme mostrado no seguinte código:

    ```
    <system.net>
        <defaultProxy useDefaultCredentials="true" />
    </system.net>
    ```

    Em seguida, pode adicionar detalhes do servidor proxy, conforme mostrado no exemplo a seguir:

    ```
    <system.net>
        <defaultProxy enabled="true">
            <proxy bypassonlocal="true" proxyaddress="http://proxy.domain.org:8888/" />
        </defaultProxy>
    </system.net>
    ```

    Propriedades adicionais são permitidas dentro da marca de proxy para especificar as definições necessárias como scriptLocation. Consulte a [proxy elemento (definições de rede)](https://msdn.microsoft.com/library/sa91de1e.aspx) sobre a sintaxe.

    ```
    <proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>
    ```
3. Guarde o ficheiro de configuração na localização original, em seguida, reinicie o serviço de anfitrião de Gateway de gestão de dados, que seleciona as alterações. Para reiniciar o serviço: usar serviços miniaplicativo do painel de controlo ou a partir do **Gestor de configuração do Data Management Gateway** > clique no **parar serviço** botão, em seguida, clique no **iniciar Serviço**. Se o serviço não iniciar, é provável que foi adicionada uma sintaxe de marca XML inválida para o ficheiro de configuração de aplicação que foi editado.

> [!IMPORTANT]
> Não se esqueça de atualizar **ambos** diahost.exe.config e diawp.exe.config.

Além desses pontos, terá também de certificar-se de que o Microsoft Azure está na lista de permissões da sua empresa. A lista de endereços válidos de IP do Microsoft Azure pode ser transferida a partir da [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=41653).

#### <a name="possible-symptoms-for-firewall-and-proxy-server-related-issues"></a>Sintomas possíveis problemas relacionados a servidores de proxy e firewall
Se encontrar erros semelhantes aos seguintes, é provável devido a uma configuração incorreta do servidor proxy ou firewall, que bloqueia o gateway se ligar ao Data Factory para se autenticar. Consulte a secção anterior para garantir que a firewall e servidor proxy estão configuradas corretamente.

1. Ao tentar registar o gateway, receberá o erro seguinte: "Falha ao registar a chave de gateway. Antes de tentar registar novamente a chave de gateway, confirme que o data management gateway está num estado ligado e o serviço de anfitrião de Gateway de gestão de dados é iniciado."
2. Quando abre o Gestor de configuração, consulte o estado como "Desligado" ou "Ligar". Ao visualizar registos de eventos do Windows, sob "Visualizador de eventos" > "Application e registos de serviços" > "Gateway de gestão de dados", verá mensagens de erro, como o seguinte erro: `Unable to connect to the remote server`
   `A component of Data Management Gateway has become unresponsive and restarts automatically. Component name: Gateway.`

### <a name="open-port-8050-for-credential-encryption"></a>Abrir a porta 8050 para encriptação de credenciais
O **definição de credenciais** aplicação utiliza a porta de entrada **8050** às credenciais de reencaminhamento para o gateway ao configurar um serviço no local ligado no portal do Azure. Durante a configuração do gateway, por predefinição, a instalação do gateway abre-a no computador gateway.

Se estiver a utilizar uma firewall de terceiros, pode abrir a porta 8050 manualmente. Caso se depare com problema do firewall durante a configuração do gateway, pode experimentar com o seguinte comando para instalar o gateway sem configurar a firewall.

    msiexec /q /i DataManagementGateway.msi NOFIREWALL=1

Se optar por não abrir a porta 8050 no computador gateway, utilize mecanismos que não a utilização a **definição credenciais** aplicativo para configurar as credenciais do arquivo de dados. Por exemplo, poderia usar [New-AzDataFactoryEncryptValue](https://docs.microsoft.com/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) cmdlet do PowerShell. Consulte a secção de credenciais de definição e a segurança na forma como as credenciais do arquivo de dados pode ser definida.

## <a name="update"></a>Atualizar
Por predefinição, o data management gateway é atualizado automaticamente quando está disponível uma versão mais recente do gateway. O gateway não é atualizado até que todas as tarefas agendadas são feitas. Não existem mais tarefas são processadas pelo gateway até a operação de atualização é concluída. Se a atualização falhar, gateway é revertido para a versão antiga.

Ver a hora da atualização agendada nos seguintes locais:

* A página de propriedades do gateway no portal do Azure.
* Home page do Data Management Gateway Configuration Manager
* Mensagem de notificação do tabuleiro de sistema.

Separador base do Data Management Gateway Configuration Manager apresenta o agendamento de atualização e a última vez que o gateway foi instalado/atualizado.

![Agendar atualizações](media/data-factory-data-management-gateway/UpdateSection.png)

Pode instalar a atualização imediatamente ou aguarde que o gateway sejam atualizadas automaticamente na hora agendada. Por exemplo, a imagem seguinte mostra a mensagem de notificação mostrada no Gestor de configuração do Gateway, juntamente com o botão de atualização que pode clicar para instalá-lo imediatamente.

![Atualização no DMG Configuration Manager](./media/data-factory-data-management-gateway/gateway-auto-update-config-manager.png)

A mensagem de notificação na Bandeja do sistema teria o aspeto conforme mostrado na imagem seguinte:

![Mensagem de tabuleiro de sistema](./media/data-factory-data-management-gateway/gateway-auto-update-tray-message.png)

Verá o estado da operação de atualização (manual ou automática) na Bandeja do sistema. Ao iniciar o Gestor de configuração do Gateway próxima vez, verá uma mensagem na barra de notificação que o gateway foi atualizado, juntamente com uma ligação para [o que há de novo tópico](data-factory-gateway-release-notes.md).

### <a name="to-disableenable-auto-update-feature"></a>Para desativar/ativar a funcionalidade de atualização automática
Pode desativar/ativar a funcionalidade de atualização automática, efetuando os seguintes passos:

[Para o gateway de nó único]
1. Inicie o Windows PowerShell no computador gateway.
2. Mude para a pasta C:\Program Files\Microsoft integração Runtime\3.0\PowerShellScript\.
3. Execute o seguinte comando para ativar a atualização automática de funcionalidades DESATIVAR (desativar).

    ```PowerShell
    .\IntegrationRuntimeAutoUpdateToggle.ps1 -off
    ```
4. Para voltar a ativá-lo:

    ```PowerShell
    .\IntegrationRuntimeAutoUpdateToggle.ps1 -on
    ```
[Para o gateway de elevada disponibilidade e dimensionável de vários nó](data-factory-data-management-gateway-high-availability-scalability.md)
1. Inicie o Windows PowerShell no computador gateway.
2. Mude para a pasta C:\Program Files\Microsoft integração Runtime\3.0\PowerShellScript\.
3. Execute o seguinte comando para ativar a atualização automática de funcionalidades DESATIVAR (desativar).

    Para o gateway com a funcionalidade de elevada disponibilidade, um parâmetro de AuthKey extra é necessário.
    ```PowerShell
    .\IntegrationRuntimeAutoUpdateToggle.ps1 -off -AuthKey <your auth key>
    ```
4. Para voltar a ativá-lo:

    ```PowerShell
    .\IntegrationRuntimeAutoUpdateToggle.ps1 -on -AuthKey <your auth key>
    ```

## <a name="configuration-manager"></a>Configuration Manager
Depois de instalar o gateway, pode iniciar o Gestor de configuração de Gateway de gestão de dados de uma das seguintes formas:

1. Na **pesquisa** janela, escreva **Data Management Gateway** para acessar esse utilitário.
2. Execute o ficheiro executável **ConfigManager.exe** na pasta: **C:\Program Files\Microsoft Data Management Gateway\2.0\Shared**

### <a name="home-page"></a>Página de boas-vindas
A Home page permite-lhe efetuar as seguintes ações:

* Ver o estado do gateway (ligado ao serviço cloud etc.).
* **Registar** através de uma chave a partir do portal.
* **Parar** e inicie o **serviço de anfitrião de Gateway de gestão de dados** no computador gateway.
* **Agendar atualizações** num momento específico dos dias.
* Ver a data quando o gateway foi **última atualização**.

### <a name="settings-page"></a>Página de definições
A página de definições permite-lhe efetuar as seguintes ações:

* Ver, alterar e exportar **certificado** utilizados pelo gateway. Este certificado é utilizado para encriptar as credenciais da origem de dados.
* Alteração **porta HTTPS** para o ponto final. O gateway abre uma porta para definir as credenciais da origem de dados.
* **Estado** do ponto final
* Modo de exibição **certificado SSL** é utilizado para comunicação SSL entre o portal e o gateway para definir credenciais para origens de dados.

### <a name="remote-access-from-intranet"></a>Acesso remoto a partir da intranet
Esta funcionalidade será ativada no futuro. Nas atualizações futuras (v3.4 ou posterior) lhe permitirá Ativar / desativar qualquer conectividade remota que ocorre hoje a utilizar a porta 8050 (consulte a secção acima), ao utilizar o PowerShell ou Gestor de credenciais do aplicativo para encriptar as credenciais.

### <a name="diagnostics-page"></a>Página de diagnóstico
A página de diagnóstico permite-lhe efetuar as seguintes ações:

* Ativar verboso **registo**, ver registos no Visualizador de eventos e enviar registos para a Microsoft se Ocorreu uma falha.
* **Testar ligação** a uma origem de dados.

### <a name="help-page"></a>Página de ajuda
A página de ajuda apresenta as seguintes informações:

* Breve descrição do gateway
* Número de versão
* Ligações para ajuda online, a declaração de privacidade e o contrato de licença.

## <a name="monitor-gateway-in-the-portal"></a>Gateway de monitorizar no portal
No portal do Azure, pode ver o instantâneo de quase em tempo real de utilização de recursos (CPU, memória, network(in/out), etc.) numa máquina de gateway.

1. No portal do Azure, navegue para a home page de sua fábrica de dados e clique em **serviços ligados** mosaico.

    ![Home page da fábrica de dados](./media/data-factory-data-management-gateway/monitor-data-factory-home-page.png)
2. Selecione o **gateway** no **serviços ligados** página.

    ![Página de serviços ligados](./media/data-factory-data-management-gateway/monitor-linked-services-blade.png)
3. Na **Gateway** página, pode ver a memória e utilização da CPU do gateway.

    ![Utilização de CPU e memória do gateway](./media/data-factory-data-management-gateway/gateway-simple-monitoring.png)
4. Ativar **definições avançadas** para ver mais detalhes, como a utilização de rede.
    
    ![Monitorização do gateway avançada](./media/data-factory-data-management-gateway/gateway-advanced-monitoring.png)

A tabela seguinte fornece descrições das colunas na **nós de Gateway** lista:

Propriedade de monitorização | Descrição
:------------------ | :----------
Name | Nome do gateway lógico e nós associado ao gateway. Nó é uma máquina com Windows no local que tem o gateway instalado no mesmo. Para obter informações sobre a ter mais de um nó (até quatro nós) num único gateway lógico, consulte [Gateway de gestão de dados - elevada disponibilidade e escalabilidade](data-factory-data-management-gateway-high-availability-scalability.md).
Estado | Estado do gateway lógico e os nós de gateway. Exemplo: Online/Offline/limitado/etc. Para obter informações sobre estes Estados terem, consulte [estado do Gateway](#gateway-status) secção.
Versão | Mostra a versão do gateway lógico e cada nó de gateway. A versão do gateway lógico é determinada com base na versão da maioria de nós no grupo. Se existirem nós com versões diferentes na configuração de gateway lógico, apenas os nós com o mesmo número de versão e a função de gateway lógico corretamente. Outras pessoas estão no modo de limitada e têm de ser atualizados manualmente (apenas no caso de falha de atualização automática).
Memória disponível | Memória disponível num nó de gateway. Este valor é um instantâneo de quase em tempo real.
Utilização da CPU | Utilização da CPU de um nó de gateway. Este valor é um instantâneo de quase em tempo real.
Funcionamento em rede (entrada/saída) | Utilização de um nó de gateway de rede. Este valor é um instantâneo de quase em tempo real.
Tarefas simultâneas (em execução / limite) | Número de tarefas ou tarefas em execução em cada nó. Este valor é um instantâneo de quase em tempo real. Limite significa o máximo de tarefas simultâneas para cada nó. Este valor é definido com base no tamanho de máquina. Pode aumentar o limite para aumentar verticalmente a execução da tarefa em simultâneo em cenários avançados, em que a CPU/memória/rede é subutilizados, mas atividades são exceder o tempo limite. Esta capacidade também está disponível com um gateway de nó único (mesmo quando a funcionalidade de escalabilidade e disponibilidade não está ativada).
Função | Existem dois tipos de funções num gateway com vários nó - Dispatcher e de trabalho. Todos os nós são funções de trabalho, o que significa que eles podem todos ser usados para executar tarefas. Há apenas um nó de dispatcher, que é utilizado para extrair tarefas/tarefas dos serviços cloud e expedi-los para nós de trabalho diferentes (incluindo ele próprio).

Nesta página, verá algumas definições que façam mais sentido quando existirem dois ou mais nós (cenário de escalamento horizontal) no gateway. Ver [Gateway de gestão de dados - elevada disponibilidade e escalabilidade](data-factory-data-management-gateway-high-availability-scalability.md) para obter detalhes sobre como configurar um gateway com vários nó.

### <a name="gateway-status"></a>Estado do gateway
A tabela seguinte fornece os Estados possíveis de uma **nó de gateway**:

Estado  | Comentários/cenários
:------- | :------------------
Online | O nó ligado ao serviço Data Factory.
Offline | O nó está offline.
A atualizar | O nó está a ser atualizados automaticamente.
Limitado | Devido a problema de conectividade. Pode ser devido a problema de 8050 de porta HTTP, o problema de conectividade de barramento de serviço ou o problema de sincronização de credenciais.
Inativa | O nó está numa configuração diferente da configuração dos outros nós da maioria.<br/><br/> Um nó pode ficar inativo, quando ele não é possível ligar a outros nós.

A tabela seguinte fornece os Estados possíveis de uma **gateway lógico**. O estado do gateway depende de Estados de nós de gateway.

Estado | Comentários
:----- | :-------
Tem de ser registado | Nenhum nó ainda está registada para este gateway lógico
Online | Nós de gateway estão online
Offline | Nenhum nó no estado online.
Limitado | Nem todos os nós neste gateway estão em bom estado de funcionamento. Este estado é um aviso de que alguns nós podem estar inativa! <br/><br/>Pode ser devido a problema de sincronização de credenciais no nó de distribuidor/trabalhador.

## <a name="scale-up-gateway"></a>Aumentar verticalmente o gateway
Pode configurar o número de **tarefas de movimento de dados em simultâneo** que podem ser executados num nó para aumentar verticalmente a capacidade de mover dados entre aplicações no local e na cloud com arquivos de dados.

Quando a memória disponível e a CPU não são utilizados bem, mas a capacidade inativa é 0, deve aumentar verticalmente ao aumento do número de tarefas simultâneas que podem ser executadas num nó. Pode também querer aumentar verticalmente quando as atividades são exceder o tempo limite porque o gateway está sobrecarregado. Nas definições avançadas de um nó de gateway, pode aumentar a capacidade máxima para um nó.

## <a name="troubleshooting-gateway-issues"></a>Resolução de problemas do gateway
Ver [resolução de problemas do gateway](data-factory-troubleshoot-gateway-issues.md) artigo para obter informações/sugestões para resolução de problemas com o data management gateway.

## <a name="move-gateway-from-one-machine-to-another"></a>Mover o gateway de uma máquina para outra
Esta secção fornece os passos para mover cliente de gateway de um computador para outro computador.

1. No portal, navegue para o **home page do Data Factory**e clique nas **serviços ligados** mosaico.

    ![Ligação de Gateways de dados](./media/data-factory-data-management-gateway/DataGatewaysLink.png)
2. Selecione o gateway no **GATEWAYS de dados** secção a **serviços ligados** página.

    ![Página de serviços ligada com o gateway selecionado](./media/data-factory-data-management-gateway/LinkedServiceBladeWithGateway.png)
3. Na **gateway de dados** página, clique em **transferir e instalar o gateway de dados**.

    ![Ligação de gateway para transferência](./media/data-factory-data-management-gateway/DownloadGatewayLink.png)
4. Na **configurar** página, clique em **transferir e instalar o gateway de dados**e siga as instruções para instalar o gateway de dados na máquina.

    ![Configurar página](./media/data-factory-data-management-gateway/ConfigureBlade.png)
5. Manter o **o Gestor de configuração do Microsoft Data Management Gateway** abrir.

    ![Configuration Manager](./media/data-factory-data-management-gateway/ConfigurationManager.png)
6. Na **configurar** página no portal, clique em **recrie chave** na barra de comandos e clique em **Sim** para a mensagem de aviso. Clique em **botão Copiar** junto ao texto da chave que copia a chave para a área de transferência. O gateway no computador antigo deixa de funcionar conforme em breve, recriar a chave.

    ![Recrie a chave](./media/data-factory-data-management-gateway/RecreateKey.png)
7. Colar o **chave** na caixa de texto no **registar Gateway** página do **Gestor de configuração do Data Management Gateway** no seu computador. (opcional) Clique em **chave de gateway Show** caixa de verificação para ver o texto da chave.

    ![Chave de cópia e registre-se](./media/data-factory-data-management-gateway/CopyKeyAndRegister.png)
8. Clique em **registar** para registar o gateway com o serviço cloud.
9. Sobre o **definições** separador, clique em **alteração** para selecionar o mesmo certificado que foi utilizado com o gateway antigo, introduza o **palavra-passe**e clique em **concluir**.

   ![Especifique o certificado](./media/data-factory-data-management-gateway/SpecifyCertificate.png)

   Pode exportar um certificado a partir do gateway antigo efetuando os seguintes passos: iniciar o Gestor de configuração do Data Management Gateway no computador antigo, mude para o **certificado** separador, clique em **exportar** e siga as instruções.
10. Após o registo com êxito do gateway, deverá ver o **Registro** definida como **registado** e **estado** definido como **introdução** na Home page do Gestor de configuração do Gateway.

## <a name="encrypting-credentials"></a>Encriptar credenciais
Para encriptar as credenciais no Editor do Data Factory, siga os passos abaixo:

1. Iniciar o navegador da web do **computador gateway**, navegue até à [portal do Azure](http://portal.azure.com). Procure a fábrica de dados, se necessário, abra a fábrica de dados no **DATA FACTORY** página e, em seguida, clique em **criar e implementar** para iniciar o Editor do Data Factory.
2. Clique num existente **serviço ligado** na vista de árvore para ver a sua definição de JSON ou criar um serviço ligado que requer um gateway de gestão de dados (por exemplo: SQL Server ou Oracle).
3. No editor de JSON, para o **gatewayName** propriedade, introduza o nome do gateway.
4. Introduza o nome de servidor para o **origem de dados** propriedade na **connectionString**.
5. Introduza o nome de base de dados para o **catálogo inicial** propriedade na **connectionString**.
6. Clique em **Encrypt** botão na barra de comando que inicia o clique-depois **Gestor de credenciais** aplicação. Deverá ver o **definição credenciais** caixa de diálogo.

    ![Caixa de diálogo de credenciais de definição](./media/data-factory-data-management-gateway/setting-credentials-dialog.png)
7. Na **definição credenciais** diálogo caixa, efetue os seguintes passos:
   1. Selecione **autenticação** que pretende que o serviço fábrica de dados a utilizar para ligar à base de dados.
   2. Introduza o nome do utilizador que tem acesso à base de dados para o **nome de utilizador** definição.
   3. Introduza a palavra-passe do utilizador para o **palavra-passe** definição.
   4. Clique em **OK** para encriptar as credenciais e fechar a caixa de diálogo.
8. Deverá ver um **encryptedCredential** propriedade na **connectionString** agora.

    ```JSON
    {
        "name": "SqlServerLinkedService",
        "properties": {
            "type": "OnPremisesSqlServer",
            "description": "",
            "typeProperties": {
                "connectionString": "data source=myserver;initial catalog=mydatabase;Integrated Security=False;EncryptedCredential=eyJDb25uZWN0aW9uU3R",
                "gatewayName": "adftutorialgateway"
            }
        }
    }
    ```
Se aceder ao portal a partir de uma máquina diferente do computador gateway, tem de certificar-se de que a aplicação do Gestor de credenciais pode ligar-se a máquina de gateway. Se o aplicativo não é possível alcançar a máquina de gateway, não permite-lhe definir as credenciais da origem de dados e para testar a ligação à origem de dados.

Quando utiliza a **definição de credenciais** aplicativo, o portal encripta as credenciais com o certificado especificado no **certificado** separador do **Gestor de configuração do Gateway**  no computador gateway.

Se estiver procurando por uma abordagem baseada em API para encriptar as credenciais, pode utilizar o [New-AzDataFactoryEncryptValue](https://docs.microsoft.com/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) cmdlet do PowerShell para encriptar as credenciais. O cmdlet utiliza o certificado que esse gateway é configurado para utilizar para encriptar as credenciais. Adicionar credenciais encriptadas para o **EncryptedCredential** elemento da **connectionString** no JSON. Utilizar o JSON com o [New-AzDataFactoryLinkedService](https://docs.microsoft.com/powershell/module/az.datafactory/new-azdatafactorylinkedservice) cmdlet ou no Editor do Data Factory.

```JSON
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```

Há uma abordagem mais para a definição de credenciais utilizando o Editor do Data Factory. Se criar um serviço ligado do SQL Server com o editor e introduza as credenciais em texto simples, as credenciais são encriptadas utilizando um certificado que o serviço Data Factory é proprietário. Não utilize o certificado que esse gateway é configurado para utilizar. Embora esta abordagem poderá ser um pouco mais rápida em alguns casos, é menos seguro. Por conseguinte, recomendamos que siga esta abordagem apenas para fins de desenvolvimento/teste.

## <a name="powershell-cmdlets"></a>Cmdlets do PowerShell
Esta secção descreve como criar e registar um gateway com cmdlets do PowerShell do Azure.

1. Inicie **do Azure PowerShell** no modo de administrador.
2. Inicie sessão na sua conta do Azure, deve executar o seguinte comando e introduzir as credenciais do Azure.

    ```PowerShell
    Connect-AzAccount
    ```
3. Utilize o **New-AzDataFactoryGateway** cmdlet para criar um gateway lógico da seguinte forma:

    ```PowerShell
    $MyDMG = New-AzDataFactoryGateway -Name <gatewayName> -DataFactoryName <dataFactoryName> -ResourceGroupName ADF –Description <desc>
    ```
    **Comando de exemplo e a saída**:

    ```
    PS C:\> $MyDMG = New-AzDataFactoryGateway -Name MyGateway -DataFactoryName $df -ResourceGroupName ADF –Description “gateway for walkthrough”

    Name              : MyGateway
    Description       : gateway for walkthrough
    Version           :
    Status            : NeedRegistration
    VersionStatus     : None
    CreateTime        : 9/28/2014 10:58:22
    RegisterTime      :
    LastConnectTime   :
    ExpiryTime        :
    ProvisioningState : Succeeded
    Key               : ADF#00000000-0000-4fb8-a867-947877aef6cb@fda06d87-f446-43b1-9485-78af26b8bab0@4707262b-dc25-4fe5-881c-c8a7c3c569fe@wu#nfU4aBlq/heRyYFZ2Xt/CD+7i73PEO521Sj2AFOCmiI
    ```

1. No Azure PowerShell, mude para a pasta: **C:\Program Files\Microsoft Data Management Gateway\2.0\PowerShellScript\**. Execute **RegisterGateway.ps1** associadas com a variável local **$Key** conforme mostrado no comando seguinte. Este script regista o agente de cliente instalado no seu computador com o gateway lógico que criou anteriormente.

    ```PowerShell
    PS C:\> .\RegisterGateway.ps1 $MyDMG.Key
    ```
    ```
    Agent registration is successful!
    ```
    Pode registar o gateway num computador remoto utilizando o parâmetro IsRegisterOnRemoteMachine. Exemplo:

    ```PowerShell
    .\RegisterGateway.ps1 $MyDMG.Key -IsRegisterOnRemoteMachine true
    ```
2. Pode utilizar o **Get-AzDataFactoryGateway** cmdlet para obter a lista de Gateways na sua fábrica de dados. Quando o **Status** mostra **online**, significa que o gateway está pronto a utilizar.

    ```PowerShell        
    Get-AzDataFactoryGateway -DataFactoryName <dataFactoryName> -ResourceGroupName ADF
    ```
Pode remover um gateway com o **Remove-AzDataFactoryGateway** cmdlet e atualização de uma descrição para um gateway com o **conjunto AzDataFactoryGateway** cmdlets. Para sintaxe e outros detalhes sobre estes cmdlets, consulte a referência de Cmdlet do Data Factory.  

### <a name="list-gateways-using-powershell"></a>Gateways de lista com o PowerShell

```PowerShell
Get-AzDataFactoryGateway -DataFactoryName jasoncopyusingstoredprocedure -ResourceGroupName ADF_ResourceGroup
```

### <a name="remove-gateway-using-powershell"></a>Remover gateway com o PowerShell

```PowerShell
Remove-AzDataFactoryGateway -Name JasonHDMG_byPSRemote -ResourceGroupName ADF_ResourceGroup -DataFactoryName jasoncopyusingstoredprocedure -Force
```

## <a name="next-steps"></a>Passos Seguintes
* Ver [mover dados entre locais e na cloud arquivos de dados](data-factory-move-data-between-onprem-and-cloud.md) artigo. No passo a passo, vai criar um pipeline que utiliza o gateway para mover dados de uma base de dados do SQL Server no local para um blob do Azure.
