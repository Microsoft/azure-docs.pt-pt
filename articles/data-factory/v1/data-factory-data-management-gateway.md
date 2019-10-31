---
title: Gerenciamento de Dados gateway para Data Factory | Microsoft Docs
description: Configure um gateway de dados para mover dados entre o local e a nuvem. Use Gerenciamento de Dados gateway no Azure Data Factory para mover seus dados.
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
ms.openlocfilehash: 0e4cf8802f9f12774f03199b76b58cb494f1c439
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73162726"
---
# <a name="data-management-gateway"></a>Data Management Gateway
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço de Data Factory, consulte [tempo de execução de integração auto-hospedado no](../create-self-hosted-integration-runtime.md).

> [!NOTE]
> O gateway de Gerenciamento de Dados agora foi remarcado como Integration Runtime hospedado internamente.

O gateway de gerenciamento de dados é um agente cliente que você deve instalar em seu ambiente local para copiar dados entre a nuvem e os armazenamentos de dados locais. Os armazenamentos de dados locais com suporte do Data Factory estão listados na seção [fontes de dados com suporte](data-factory-data-movement-activities.md#supported-data-stores-and-formats) .

Este artigo complementa o passo a passos no artigo [mover dados entre locais e armazenamentos de dados na nuvem](data-factory-move-data-between-onprem-and-cloud.md) . No passo a passo, você cria um pipeline que usa o gateway para mover dados de um banco de SQL Server local para um blob do Azure. Este artigo fornece informações detalhadas sobre o gateway de gerenciamento de dados.

Você pode escalar horizontalmente um gateway de gerenciamento de dados associando vários computadores locais ao gateway. Você pode escalar verticalmente aumentando o número de trabalhos de movimentação de dados que podem ser executados simultaneamente em um nó. Esse recurso também está disponível para um gateway lógico com um único nó. Consulte [dimensionar o gateway de gerenciamento de dados no artigo Azure data Factory](data-factory-data-management-gateway-high-availability-scalability.md) para obter detalhes.

> [!NOTE]
> Atualmente, o gateway oferece suporte apenas para a atividade de cópia e atividade de procedimento armazenado no Data Factory. Não é possível usar o gateway de uma atividade personalizada para acessar fontes de dados locais.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Visão geral
### <a name="capabilities-of-data-management-gateway"></a>Recursos do gateway de gerenciamento de dados
O gateway de gerenciamento de dados fornece os seguintes recursos:

* Modele fontes de dados locais e fontes de dados na nuvem dentro do mesmo data factory e mover dados.
* Tenha um único painel para monitoramento e gerenciamento com visibilidade do status do gateway na página Data Factory.
* Gerencie o acesso a fontes de dados locais com segurança.
  * Nenhuma alteração é necessária para o firewall corporativo. O gateway faz apenas conexões de saída baseadas em HTTP para abrir a Internet.
  * Criptografe as credenciais para seus armazenamentos de dados locais com seu certificado.
* Mover dados com eficiência – os dados são transferidos em paralelo, resilientes a problemas de rede intermitentes com a lógica de repetição automática.

### <a name="command-flow-and-data-flow"></a>Fluxo de comando e fluxo de dados
Quando você usa uma atividade de cópia para copiar dados entre o local e a nuvem, a atividade usa um gateway para transferir dados da fonte de dados local para a nuvem e vice-versa.

Aqui está o fluxo de dados de alto nível para o e o resumo das etapas para cópia com o gateway de dados: ![fluxo de dados usando o gateway](./media/data-factory-data-management-gateway/data-flow-using-gateway.png)

1. O desenvolvedor de dados cria um gateway para um Azure Data Factory usando o cmdlet [portal do Azure](https://portal.azure.com) ou [PowerShell](https://docs.microsoft.com/powershell/module/az.datafactory/).
2. O desenvolvedor de dados cria um serviço vinculado para um armazenamento de dados local, especificando o gateway. Como parte da configuração do serviço vinculado, o desenvolvedor de dados usa o aplicativo de configuração de credenciais para especificar os tipos de autenticação e as credenciais. A caixa de diálogo configuração do aplicativo de credenciais se comunica com o armazenamento de dados para testar a conexão e o gateway para salvar as credenciais.
3. O gateway criptografa as credenciais com o certificado associado ao gateway (fornecido pelo desenvolvedor de dados) antes de salvar as credenciais na nuvem.
4. Data Factory serviço se comunica com o gateway para o agendamento de & gerenciamento de trabalhos por meio de um canal de controle que usa uma fila do barramento de serviço do Azure compartilhada. Quando um trabalho de atividade de cópia precisa ser iniciado, o Data Factory enfileira a solicitação junto com as informações de credenciais. O gateway inicia o trabalho depois de sondar a fila.
5. O gateway descriptografa as credenciais com o mesmo certificado e, em seguida, conecta-se ao armazenamento de dados local com o tipo de autenticação e as credenciais adequadas.
6. O gateway copia dados de um repositório local para um armazenamento em nuvem, ou vice-versa, dependendo de como a atividade de cópia está configurada no pipeline de dados. Para esta etapa, o gateway se comunica diretamente com serviços de armazenamento baseados em nuvem, como o armazenamento de BLOBs do Azure em um canal seguro (HTTPS).

### <a name="considerations-for-using-gateway"></a>Considerações para usar o gateway
* Uma única instância do gateway de gerenciamento de dados pode ser usada para várias fontes de dados locais. No entanto, **uma única instância de gateway é vinculada a apenas uma data Factory do Azure** e não pode ser compartilhada com outra data Factory.
* Você pode ter **apenas uma instância do gateway de gerenciamento de dados** instalada em um único computador. Suponha que você tenha duas data factories que precisam acessar fontes de dados locais, você precisa instalar gateways em dois computadores locais. Em outras palavras, um gateway está vinculado a um data factory específico
* O **Gateway não precisa estar no mesmo computador que a fonte de dados**. No entanto, ter um gateway mais próximo da fonte de dados reduz o tempo para o gateway se conectar à fonte de dados. Recomendamos que você instale o gateway em um computador diferente daquele que hospeda a fonte de dados local. Quando o gateway e a fonte de dados estiverem em computadores diferentes, o gateway não concorrerá por recursos com a fonte de dados.
* Você pode ter **vários gateways em computadores diferentes que se conectam à mesma fonte de dados local**. Por exemplo, você pode ter dois gateways servindo duas fábricas de dados, mas a mesma fonte de dados local é registrada com ambas as fábricas de dados.
* Se você já tiver um gateway instalado no computador que atende a um cenário de **Power bi** , instale um **gateway separado para Azure data Factory** em outro computador.
* O gateway deve ser usado mesmo quando você usa o **ExpressRoute**.
* Trate sua fonte de dados como uma fonte de dados local (que está atrás de um firewall) mesmo quando você usa o **ExpressRoute**. Use o gateway para estabelecer a conectividade entre o serviço e a fonte de dados.
* Você deve **usar o gateway** mesmo se o armazenamento de dados estiver na nuvem em uma **VM IaaS do Azure**.

## <a name="installation"></a>Instalação
### <a name="prerequisites"></a>Pré-requisitos
* As versões de **sistema operacional** com suporte são Windows 7, Windows 8/8.1, Windows 10, windows Server 2008 R2, windows Server 2012, windows Server 2012 R2. Atualmente, não há suporte para a instalação do gateway de gerenciamento de dados em um controlador de domínio.
* O .NET Framework 4.5.1 ou superior é necessário. Se você estiver instalando o gateway em um computador com o Windows 7, instale o .NET Framework 4,5 ou posterior. Consulte [.NET Framework requisitos do sistema](https://msdn.microsoft.com/library/8z6watww.aspx) para obter detalhes.
* A **configuração** recomendada para o computador do gateway é de pelo menos 2 GHz, 4 núcleos, 8 GB de RAM e disco de 80 GB.
* Se o computador host hibernar, o gateway não responderá às solicitações de dados. Portanto, configure um **plano de energia** apropriado no computador antes de instalar o gateway. Se o computador estiver configurado para hibernar, a instalação do gateway solicitará uma mensagem.
* Você deve ser um administrador no computador para instalar e configurar o gateway de gerenciamento de dados com êxito. Você pode adicionar outros usuários ao grupo local do Windows **usuários do gateway de gerenciamento de dados** . Os membros desse grupo são capazes de usar a ferramenta de **Configuration Manager do gerenciamento de dados gateway** para configurar o gateway.

À medida que as execuções de atividade de cópia acontecem em uma frequência específica, o uso de recursos (CPU, memória) no computador também segue o mesmo padrão com tempos ociosos e de pico. A utilização de recursos também depende muito da quantidade de dados que estão sendo movidos. Quando vários trabalhos de cópia estiverem em andamento, você verá o uso do recurso subir durante horários de pico.

### <a name="installation-options"></a>Opções de instalação
O gateway de gerenciamento de dados pode ser instalado das seguintes maneiras:

* Baixando um pacote de instalação MSI do [centro de download da Microsoft](https://www.microsoft.com/download/details.aspx?id=39717). O MSI também pode ser usado para atualizar o gateway de gerenciamento de dados existente para a versão mais recente, com todas as configurações preservadas.
* Clicando em **baixar e instalar** o link do gateway de dados em configuração manual ou **instalar diretamente neste computador** em instalação expressa. Confira o artigo [mover dados entre o local e a nuvem](data-factory-move-data-between-onprem-and-cloud.md) para obter instruções passo a passo sobre como usar a instalação expressa. A etapa manual leva você para o centro de download. As instruções para baixar e instalar o gateway do centro de download estão na próxima seção.

### <a name="installation-best-practices"></a>Práticas recomendadas de instalação:
1. Configure o plano de energia no computador host para o gateway para que o computador não faça hibernação. Se o computador host hibernar, o gateway não responderá às solicitações de dados.
2. Faça backup do certificado associado ao gateway.

### <a name="install-the-gateway-from-download-center"></a>Instalar o gateway do centro de download
1. Navegue até a [página de download do Microsoft gerenciamento de dados gateway](https://www.microsoft.com/download/details.aspx?id=39717).
2. Clique em **baixar**, selecione a versão de **64 bits** (não há mais suporte para 32 bits) e clique em **Avançar**.
3. Execute o **MSI** diretamente ou salve-o no disco rígido e execute-o.
4. Na página de **boas-vindas** , selecione um **idioma** e clique em **Avançar**.
5. **Aceite** o contrato de licença de usuário final e clique em **Avançar**.
6. Selecione a **pasta** para instalar o gateway e clique em **Avançar**.
7. Na página **pronto para instalar** , clique em **instalar**.
8. Clique em **concluir** para concluir a instalação.
9. Obtenha a chave do portal do Azure. Consulte a próxima seção para obter instruções passo a passo.
10. Na página **registrar gateway** do **Gerenciamento de dados gateway Configuration Manager** em execução no seu computador, execute as seguintes etapas:
    1. Cole a chave no texto.
    2. Opcionalmente, clique em **Mostrar chave do gateway** para ver o texto da chave.
    3. Clique em **registrar**.

### <a name="register-gateway-using-key"></a>Registrar o gateway usando a chave
#### <a name="if-you-havent-already-created-a-logical-gateway-in-the-portal"></a>Se você ainda não criou um gateway lógico no portal
Para criar um gateway no portal e obter a chave na página **Configurar** , siga as etapas de passo a passos no artigo [mover dados entre o local e a nuvem](data-factory-move-data-between-onprem-and-cloud.md) .

#### <a name="if-you-have-already-created-the-logical-gateway-in-the-portal"></a>Se você já tiver criado o gateway lógico no portal
1. Em portal do Azure, navegue até a página **Data Factory** e clique no bloco **Serviços vinculados** .

    ![Página de Data Factory](media/data-factory-data-management-gateway/data-factory-blade.png)
2. Na página **Serviços vinculados** , selecione o **Gateway** lógico que você criou no Portal.

    ![Gateway lógico](media/data-factory-data-management-gateway/data-factory-select-gateway.png)
3. Na página **Gateway de dados** , clique em **baixar e instalar o gateway de dados**.

    ![Link de download no portal](media/data-factory-data-management-gateway/download-and-install-link-on-portal.png)
4. Na página **Configurar** , clique em **recriar chave**. Clique em Sim na mensagem de aviso depois de lê-la com cuidado.

    ![Recriar chave](media/data-factory-data-management-gateway/recreate-key-button.png)
5. Clique no botão copiar ao lado da chave. A chave é copiada para a área de transferência.

    ![Chave de cópia](media/data-factory-data-management-gateway/copy-gateway-key.png)

### <a name="system-tray-icons-notifications"></a>Ícones/notificações da bandeja do sistema
A imagem a seguir mostra alguns dos ícones de bandeja que você vê.

![ícones da bandeja do sistema](./media/data-factory-data-management-gateway/gateway-tray-icons.png)

Se você mover o cursor sobre a mensagem de ícone/notificação da bandeja do sistema, verá detalhes sobre o estado da operação de gateway/atualização em uma janela pop-up.

### <a name="ports-and-firewall"></a>Portas e firewall
Há dois firewalls que você precisa considerar: o **firewall corporativo** em execução no roteador central da organização e o **Firewall do Windows** configurados como um daemon no computador local em que o gateway está instalado.

![firewalls](./media/data-factory-data-management-gateway/firewalls2.png)

No nível de firewall corporativo, você precisa configurar os seguintes domínios e portas de saída:

| Nomes de domínio | Portas | Descrição |
| --- | --- | --- |
| *.servicebus.windows.net |443 |Usado para comunicação com o back-end do serviço de movimentação de dados |
| *. core.windows.net |443 |Usado para cópia em etapas usando o blob do Azure (se configurado)|
| *. frontend.clouddatahub.net |443 |Usado para comunicação com o back-end do serviço de movimentação de dados |
| *.servicebus.windows.net |9350-9354, 5671 |Retransmissão do barramento de serviço opcional sobre TCP usado pelo assistente de cópia |

No nível do firewall do Windows, essas portas de saída são normalmente habilitadas. Caso contrário, você pode configurar os domínios e as portas adequadamente no computador do gateway.

> [!NOTE]
> 1. Com base em sua fonte/coletores, você pode ter que adicionar domínios e portas de saída à lista de permissões em seu firewall corporativo/Windows.
> 2. Para alguns bancos de dados de nuvem (por exemplo: [banco de dados SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-configure-firewall-settings), [Azure data Lake](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-secure-data#set-ip-address-range-for-data-access), etc.), talvez seja necessário o endereço IP da lista de permissões do computador do gateway na configuração do firewall.
>
>

#### <a name="copy-data-from-a-source-data-store-to-a-sink-data-store"></a>Copiar dados de um armazenamento de dados de origem para um armazenamento de dados de coletor
Verifique se as regras de firewall estão habilitadas corretamente no firewall corporativo, no firewall do Windows no computador do gateway e no próprio repositório de dados. A habilitação dessas regras permite que o gateway se conecte à fonte e ao coletor com êxito. Habilite regras para cada armazenamento de dados envolvido na operação de cópia.

Por exemplo, para copiar de **um armazenamento de dados local para um coletor de banco de dados SQL do Azure ou um coletor de SQL data warehouse do Azure**, execute as seguintes etapas:

* Permita a comunicação **TCP** de saída na porta **1433** para o Firewall do Windows e o firewall corporativo.
* Defina as configurações de firewall do SQL Server do Azure para adicionar o endereço IP do computador do gateway à lista de endereços IP permitidos.

> [!NOTE]
> Se o firewall não permitir a porta de saída 1433, o gateway não poderá acessar diretamente o SQL do Azure. Nesse caso, você pode usar a [cópia em etapas](https://docs.microsoft.com/azure/data-factory/data-factory-copy-activity-performance#staged-copy) para SQL Azure banco de dados/SQL Azure DW. Nesse cenário, você só precisaria de HTTPS (porta 443) para a movimentação de dados.
>
>

### <a name="proxy-server-considerations"></a>Considerações sobre o servidor proxy
Se o ambiente de rede corporativa usar um servidor proxy para acessar a Internet, configure o gateway de gerenciamento de dados para usar as configurações de proxy apropriadas. Você pode definir o proxy durante a fase de registro inicial.

![Definir o proxy durante o registro](media/data-factory-data-management-gateway/SetProxyDuringRegistration.png)

O gateway usa o servidor proxy para se conectar ao serviço de nuvem. Clique em **alterar** link durante a configuração inicial. Você verá a caixa de diálogo de **configuração de proxy** .

![Definir proxy usando o Gerenciador de configuração](media/data-factory-data-management-gateway/SetProxySettings.png)

Há três opções de configuração:

* Não **usar proxy**: o gateway não usa explicitamente nenhum proxy para se conectar aos serviços de nuvem.
* **Usar o proxy do sistema**: o gateway usa a configuração de proxy configurada em diahost. exe. config e diawp. exe. config. Se nenhum proxy estiver configurado em diahost. exe. config e diawp. exe. config, o gateway se conectará ao serviço de nuvem diretamente sem passar pelo proxy.
* **Usar proxy personalizado**: defina a configuração de proxy http a ser usada para o gateway, em vez de usar as configurações em diahost. exe. config e diawp. exe. config. O endereço e a porta são necessários. O nome de usuário e a senha são opcionais, dependendo da configuração de autenticação do proxy. Todas as configurações são criptografadas com o certificado de credencial do gateway e armazenadas localmente no computador host do gateway.

O serviço de host do gateway de gerenciamento de dados é reiniciado automaticamente depois que você salva as configurações de proxy atualizadas.

Depois que o gateway tiver sido registrado com êxito, se você quiser exibir ou atualizar as configurações de proxy, use Gerenciamento de Dados gateway Configuration Manager.

1. Iniciar **Configuration Manager do gateway de gerenciamento de dados**.
2. Mudar para o separador **Definições**.
3. Clique no link **alterar** na seção **proxy http** para iniciar a caixa de diálogo **Definir proxy http** .
4. Depois de clicar no botão **Avançar** , você verá uma caixa de diálogo de aviso solicitando sua permissão para salvar a configuração de proxy e reiniciar o serviço de host do gateway.

Você pode exibir e atualizar o proxy HTTP usando a ferramenta Configuration Manager.

![Definir proxy usando o Gerenciador de configuração](media/data-factory-data-management-gateway/SetProxyConfigManager.png)

> [!NOTE]
> Se você configurar um servidor proxy com autenticação NTLM, o serviço de host do gateway será executado na conta de domínio. Se você alterar a senha para a conta de domínio mais tarde, lembre-se de atualizar as definições de configuração para o serviço e reiniciá-lo de acordo. Devido a esse requisito, sugerimos que você use uma conta de domínio dedicada para acessar o servidor proxy que não exige que você atualize a senha com frequência.
>
>

### <a name="configure-proxy-server-settings"></a>Definir configurações do servidor proxy
Se você selecionar **usar** configuração de proxy do sistema para o proxy http, o gateway usará a configuração de proxy em diahost. exe. config e diawp. exe. config. Se nenhum proxy for especificado em diahost. exe. config e diawp. exe. config, o gateway se conectará ao serviço de nuvem diretamente sem passar pelo proxy. O procedimento a seguir fornece instruções para atualizar o arquivo diahost. exe. config.

1. No explorador de arquivos, faça uma cópia segura de *arquivos de programas C:\\\\\\Microsoft gerenciamento de dados Gateway\\2,0\\Shared\\diahost. exe. config* para fazer backup do arquivo original.
2. Inicie o notepad. exe em execução como administrador e abra o arquivo de texto *C:\\\\arquivos de programa\\Microsoft gerenciamento de dados Gateway\\2,0\\Shared\\diahost. exe. config*. Você encontra a marca padrão para system.net, conforme mostrado no código a seguir:

    ```
    <system.net>
        <defaultProxy useDefaultCredentials="true" />
    </system.net>
    ```

    Em seguida, você pode adicionar detalhes do servidor proxy, conforme mostrado no exemplo a seguir:

    ```
    <system.net>
        <defaultProxy enabled="true">
            <proxy bypassonlocal="true" proxyaddress="http://proxy.domain.org:8888/" />
        </defaultProxy>
    </system.net>
    ```

    Propriedades adicionais são permitidas dentro da marca de proxy para especificar as configurações necessárias, como scriptLocation. Consulte o [elemento proxy (configurações de rede)](https://msdn.microsoft.com/library/sa91de1e.aspx) na sintaxe.

    ```
    <proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>
    ```
3. Salve o arquivo de configuração no local original e reinicie o Gerenciamento de Dados serviço de host do gateway, que pega as alterações. Para reiniciar o serviço: Use o applet de serviços no painel de controle ou, no **Gerenciamento de dados Gateway Configuration Manager** > clique no botão **parar serviço** e, em seguida, clique no **serviço iniciar**. Se o serviço não iniciar, é provável que uma sintaxe de marca XML incorreta tenha sido adicionada ao arquivo de configuração do aplicativo que foi editado.

> [!IMPORTANT]
> Não se esqueça **de atualizar diahost** . exe. config e diawp. exe. config.

Além desses pontos, você também precisa ter certeza de que Microsoft Azure está na lista de permissões da sua empresa. A lista de endereços IP de Microsoft Azure válidos pode ser baixada no [centro de download da Microsoft](https://www.microsoft.com/download/details.aspx?id=41653).

#### <a name="possible-symptoms-for-firewall-and-proxy-server-related-issues"></a>Possíveis sintomas para problemas relacionados ao firewall e ao servidor proxy
Se você encontrar erros semelhantes aos seguintes, provavelmente, devido à configuração incorreta do firewall ou do servidor proxy, que impede que o gateway se conecte ao Data Factory para se autenticar. Consulte a seção anterior para garantir que o firewall e o servidor proxy estejam configurados corretamente.

1. Ao tentar registrar o gateway, você recebe o seguinte erro: "falha ao registrar a chave do gateway. Antes de tentar registrar a chave do gateway novamente, confirme se o gateway de gerenciamento de dados está em um estado conectado e se o serviço de host do gateway de Gerenciamento de Dados foi iniciado. "
2. Ao abrir Configuration Manager, você verá status como "desconectado" ou "conectando". Ao exibir logs de eventos do Windows, em "Visualizador de Eventos" > "logs de aplicativos e serviços" > "Gerenciamento de Dados gateway", você verá mensagens de erro como o seguinte erro: `Unable to connect to the remote server`
   `A component of Data Management Gateway has become unresponsive and restarts automatically. Component name: Gateway.`

### <a name="open-port-8050-for-credential-encryption"></a>Abrir a porta 8050 para criptografia de credencial
O aplicativo **Setting Credentials** usa a porta de entrada **8050** para retransmitir credenciais para o gateway quando você configura um serviço vinculado local no portal do Azure. Durante a configuração do gateway, por padrão, a instalação do gateway a abre no computador do gateway.

Se você estiver usando um firewall de terceiros, poderá abrir manualmente a porta 8050. Se você tiver um problema de firewall durante a instalação do gateway, poderá tentar usar o comando a seguir para instalar o gateway sem configurar o firewall.

    msiexec /q /i DataManagementGateway.msi NOFIREWALL=1

Se você optar por não abrir a porta 8050 no computador do gateway, use mecanismos diferentes de usar o aplicativo **Setting Credentials** para configurar as credenciais do armazenamento de dados. Por exemplo, você pode usar o cmdlet do PowerShell [New-AzDataFactoryEncryptValue](https://docs.microsoft.com/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) . Consulte a seção definindo credenciais e segurança sobre como as credenciais do armazenamento de dados podem ser definidas.

## <a name="update"></a>Atualizar
Por padrão, o gateway de gerenciamento de dados é atualizado automaticamente quando uma versão mais recente do gateway está disponível. O gateway não é atualizado até que todas as tarefas agendadas sejam concluídas. Nenhuma tarefa adicional será processada pelo gateway até que a operação de atualização seja concluída. Se a atualização falhar, o gateway será revertido para a versão antiga.

Você verá a hora de atualização agendada nos seguintes locais:

* A página Propriedades do gateway na portal do Azure.
* Home Page do gateway de Gerenciamento de Dados Configuration Manager
* Mensagem de notificação da bandeja do sistema.

A guia início do Gerenciamento de Dados gateway Configuration Manager exibe a agenda de atualização e a última vez em que o gateway foi instalado/atualizado.

![Atualizações agendadas](media/data-factory-data-management-gateway/UpdateSection.png)

Você pode instalar a atualização imediatamente ou aguardar até que o gateway seja atualizado automaticamente no horário agendado. Por exemplo, a imagem a seguir mostra a mensagem de notificação mostrada no Configuration Manager do gateway junto com o botão atualizar, no qual você pode clicar para instalá-lo imediatamente.

![Atualizar no DMG Configuration Manager](./media/data-factory-data-management-gateway/gateway-auto-update-config-manager.png)

A mensagem de notificação na bandeja do sistema seria semelhante à mostrada na imagem a seguir:

![Mensagem da bandeja do sistema](./media/data-factory-data-management-gateway/gateway-auto-update-tray-message.png)

Você verá o status da operação de atualização (manual ou automática) na bandeja do sistema. Ao iniciar o gateway Configuration Manager da próxima vez, você verá uma mensagem na barra de notificação de que o gateway foi atualizado junto com um link para [o tópico o que há de novo](data-factory-gateway-release-notes.md).

### <a name="to-disableenable-auto-update-feature"></a>Para desabilitar/habilitar o recurso de atualização automática
Você pode desabilitar/habilitar o recurso de atualização automática executando as seguintes etapas:

[Para gateway de nó único]
1. Inicie o Windows PowerShell no computador do gateway.
2. Alterne para a pasta *C:\\\\Program files\\Microsoft Integration Runtime\\3,0\\PowerShellScript\\* .
3. Execute o comando a seguir para desativar o recurso de atualização automática (desabilitar).

    ```powershell
    .\IntegrationRuntimeAutoUpdateToggle.ps1 -off
    ```
4. Para ativá-lo novamente:

    ```powershell
    .\IntegrationRuntimeAutoUpdateToggle.ps1 -on
    ```
   [Para vários nós altamente disponíveis e gateway escalonável](data-factory-data-management-gateway-high-availability-scalability.md)
1. Inicie o Windows PowerShell no computador do gateway.
2. Alterne para a pasta *C:\\\\Program files\\Microsoft Integration Runtime\\3,0\\PowerShellScript\\* .
3. Execute o comando a seguir para desativar o recurso de atualização automática (desabilitar).

    Para o gateway com o recurso de alta disponibilidade, é necessário um parâmetro AuthKey extra.
    ```powershell
    .\IntegrationRuntimeAutoUpdateToggle.ps1 -off -AuthKey <your auth key>
    ```
4. Para ativá-lo novamente:

    ```powershell
    .\IntegrationRuntimeAutoUpdateToggle.ps1 -on -AuthKey <your auth key>
    ```

## <a name="configuration-manager"></a>Configuration Manager
Depois de instalar o gateway, você pode iniciar o Gerenciamento de Dados gateway Configuration Manager de uma das seguintes maneiras:

1. Na janela de **pesquisa** , digite **Gerenciamento de dados gateway** para acessar esse utilitário.
2. Execute o executável *configmanager. exe* na pasta: *C:\\\\arquivos de programas\\Microsoft Gerenciamento de Dados Gateway\\2,0\\compartilhado*.

### <a name="home-page"></a>Página de boas-vindas
A Home Page permite que você execute as seguintes ações:

* Exibir o status do gateway (conectado ao serviço de nuvem, etc.).
* **Registre-se** usando uma chave do Portal.
* **Pare** e inicie o **serviço de Host de gateway gerenciamento de dados** no computador do gateway.
* **Agendar atualizações** em um momento específico dos dias.
* Exiba a data em que o gateway foi **atualizado pela última vez**.

### <a name="settings-page"></a>Página de definições
A página configurações permite que você execute as seguintes ações:

* Exiba, altere e exporte o **certificado** usado pelo Gateway. Este certificado é usado para criptografar credenciais de fonte de dados.
* Altere a **porta https** para o ponto de extremidade. O gateway abre uma porta para definir as credenciais da fonte de dados.
* **Status** do ponto de extremidade
* A exibição do **certificado SSL** é usada para comunicação SSL entre o portal e o gateway para definir credenciais para fontes de dados.

### <a name="remote-access-from-intranet"></a>Acesso remoto da intranet
Essa funcionalidade será habilitada no futuro. Nas próximas atualizações (v 3.4 ou posterior), permitiremos habilitar/desabilitar qualquer conectividade remota que hoje ocorra usando a porta 8050 (consulte a seção acima) ao usar o PowerShell ou o aplicativo Gerenciador de credenciais para criptografar credenciais.

### <a name="diagnostics-page"></a>Página de diagnóstico
A página diagnóstico permite que você execute as seguintes ações:

* Habilite o **log**detalhado, exiba os logs no Visualizador de eventos e envie os logs para a Microsoft se houvesse uma falha.
* **Testar a conexão** com uma fonte de dados.

### <a name="help-page"></a>Página de ajuda
A página de ajuda exibe as seguintes informações:

* Breve descrição do gateway
* Número de versão
* Links para a ajuda online, política de privacidade e contrato de licença.

## <a name="monitor-gateway-in-the-portal"></a>Monitorar o gateway no portal
No portal do Azure, você pode exibir o instantâneo quase em tempo real da utilização de recursos (CPU, memória, rede (entrada/saída), etc.) em um computador de gateway.

1. Em portal do Azure, navegue até o home page para seu data factory e clique no bloco **Serviços vinculados** .

    ![Home page da fábrica de dados](./media/data-factory-data-management-gateway/monitor-data-factory-home-page.png)
2. Selecione o **Gateway** na página **Serviços vinculados** .

    ![Página de serviços vinculados](./media/data-factory-data-management-gateway/monitor-linked-services-blade.png)
3. Na página **Gateway** , você pode ver a memória e o uso da CPU do gateway.

    ![Uso de CPU e memória do gateway](./media/data-factory-data-management-gateway/gateway-simple-monitoring.png)
4. Habilite **Configurações avançadas** para ver mais detalhes, como o uso da rede.
    
    ![Monitoramento avançado do gateway](./media/data-factory-data-management-gateway/gateway-advanced-monitoring.png)

A tabela a seguir fornece descrições das colunas na lista **nós de gateway** :

Propriedade de monitoramento | Descrição
:------------------ | :----------
Nome | Nome do gateway lógico e nós associados ao gateway. O nó é um computador local do Windows que tem o gateway instalado. Para obter informações sobre como ter mais de um nó (até quatro nós) em um único gateway lógico, consulte [Gateway de gerenciamento de dados – alta disponibilidade e escalabilidade](data-factory-data-management-gateway-high-availability-scalability.md).
Estado | Status do gateway lógico e dos nós de gateway. Exemplo: online/offline/Limited/etc. Para obter informações sobre esses status, consulte a seção [status do gateway](#gateway-status) .
Versão | Mostra a versão do gateway lógico e cada nó de gateway. A versão do gateway lógico é determinada com base na versão da maioria dos nós no grupo. Se houver nós com versões diferentes na configuração do gateway lógico, somente os nós com o mesmo número de versão que o gateway lógico funcionarão corretamente. Outras estão no modo limitado e precisam ser atualizadas manualmente (somente em caso de falha de atualização automática).
Memória disponível | Memória disponível em um nó de gateway. Esse valor é um instantâneo quase em tempo real.
Utilização da CPU | Utilização da CPU de um nó de gateway. Esse valor é um instantâneo quase em tempo real.
Rede (entrada/saída) | Utilização de rede de um nó de gateway. Esse valor é um instantâneo quase em tempo real.
Trabalhos simultâneos (em execução/limite) | Número de trabalhos ou tarefas em execução em cada nó. Esse valor é um instantâneo quase em tempo real. Limit significa o máximo de trabalhos simultâneos para cada nó. Esse valor é definido com base no tamanho da máquina. Você pode aumentar o limite para escalar verticalmente a execução de trabalhos simultâneos em cenários avançados, em que a CPU/memória/rede está subutilizada, mas as atividades estão atingindo o tempo limite. Esse recurso também está disponível com um gateway de nó único (mesmo quando o recurso de escalabilidade e disponibilidade não está habilitado).
Função | Há dois tipos de funções em um gateway de vários nós-Dispatcher e trabalho. Todos os nós são trabalhadores, o que significa que eles podem ser usados para executar trabalhos. Há apenas um nó Dispatcher, que é usado para efetuar pull de tarefas/trabalhos dos serviços de nuvem e expedir-os para diferentes nós de trabalho (incluindo ele mesmo).

Nesta página, você verá algumas configurações que fazem mais sentido quando há dois ou mais nós (cenário de expansão) no gateway. Consulte [Gerenciamento de dados gateway-alta disponibilidade e escalabilidade](data-factory-data-management-gateway-high-availability-scalability.md) para obter detalhes sobre como configurar um gateway de vários nós.

### <a name="gateway-status"></a>Status do gateway
A tabela a seguir fornece os possíveis status de um **nó de gateway**:

Estado  | Comentários/cenários
:------- | :------------------
Online | Nó conectado ao serviço de Data Factory.
Está | O nó está offline.
Upgrade | O nó está sendo atualizado automaticamente.
Limitado | Devido a um problema de conectividade. Pode ser devido ao problema de porta HTTP 8050, problema de conectividade do barramento de serviço ou problema de sincronização de credenciais.
Inativo | O nó está em uma configuração diferente da configuração de outros nós de maioria.<br/><br/> Um nó pode ficar inativo quando não puder se conectar a outros nós.

A tabela a seguir fornece os possíveis status de um **Gateway lógico**. O status do gateway depende dos status dos nós do gateway.

Estado | Comentários
:----- | :-------
Precisa de registro | Nenhum nó ainda está registrado para este gateway lógico
Online | Os nós de gateway estão online
Está | Nenhum nó no status online.
Limitado | Nem todos os nós neste gateway estão em estado íntegro. Esse status é um aviso de que algum nó pode estar inoperante! <br/><br/>Pode ser devido a um problema de sincronização de credenciais no nó Dispatcher/de trabalho.

## <a name="scale-up-gateway"></a>Escalar verticalmente o gateway
Você pode configurar o número de **trabalhos de movimentação de dados simultâneos** que podem ser executados em um nó para escalar verticalmente a capacidade de mover dados entre armazenamentos de dados locais e na nuvem.

Quando a memória disponível e a CPU não são utilizadas bem, mas a capacidade ociosa é 0, você deve escalar verticalmente aumentando o número de trabalhos simultâneos que podem ser executados em um nó. Você também pode querer escalar verticalmente quando as atividades esgotam o tempo limite porque o gateway está sobrecarregado. Nas configurações avançadas de um nó de gateway, você pode aumentar a capacidade máxima de um nó.

## <a name="troubleshooting-gateway-issues"></a>Solucionando problemas de gateway
Consulte o artigo [Solucionando](data-factory-troubleshoot-gateway-issues.md) problemas do gateway para obter informações/Dicas para solução de problemas com o uso do gateway de gerenciamento de dados.

## <a name="move-gateway-from-one-machine-to-another"></a>Mover o gateway de um computador para outro
Esta seção fornece etapas para mover o cliente de gateway de um computador para outro computador.

1. No portal, navegue até o **Data Factory Home Page**e clique no bloco **Serviços vinculados** .

    ![Link de gateways de dados](./media/data-factory-data-management-gateway/DataGatewaysLink.png)
2. Selecione o gateway na seção **gateways de dados** da página **Serviços vinculados** .

    ![Página de serviços vinculados com gateway selecionado](./media/data-factory-data-management-gateway/LinkedServiceBladeWithGateway.png)
3. Na página **Gateway de dados** , clique em **baixar e instalar o gateway de dados**.

    ![Link do gateway de download](./media/data-factory-data-management-gateway/DownloadGatewayLink.png)
4. Na página **Configurar** , clique em **baixar e instalar o gateway de dados**e siga as instruções para instalar o gateway de dados no computador.

    ![Configurar página](./media/data-factory-data-management-gateway/ConfigureBlade.png)
5. Mantenha o **gateway do Microsoft Gerenciamento de Dados Configuration Manager** aberto.

    ![Configuration Manager](./media/data-factory-data-management-gateway/ConfigurationManager.png)
6. Na página **Configurar** no portal, clique em **recriar chave** na barra de comandos e clique em **Sim** para a mensagem de aviso. Clique no **botão Copiar** ao lado de texto de chave que copia a chave para a área de transferência. O gateway no computador antigo para de funcionar assim que você recriar a chave.

    ![Recriar chave](./media/data-factory-data-management-gateway/RecreateKey.png)
7. Cole a **chave** na caixa de texto na página **registrar gateway** do **Gerenciamento de dados gateway Configuration Manager** em seu computador. adicional Clique na caixa de seleção **Mostrar chave do gateway** para ver o texto da chave.

    ![Copiar chave e registrar](./media/data-factory-data-management-gateway/CopyKeyAndRegister.png)
8. Clique em **registrar** para registrar o gateway com o serviço de nuvem.
9. Na guia **configurações** , clique em **alterar** para selecionar o mesmo certificado que foi usado com o gateway antigo, digite a **senha**e clique em **concluir**.

   ![Especificar certificado](./media/data-factory-data-management-gateway/SpecifyCertificate.png)

   Você pode exportar um certificado do gateway antigo executando as seguintes etapas: iniciar Gerenciamento de Dados gateway Configuration Manager no computador antigo, alterne para a guia **certificado** , clique no botão **Exportar** e siga as instruções.
10. Após o registro bem-sucedido do gateway, você deverá ver o **registro** definido como **registrado** e o **status** definido como **iniciado** na home page do gateway Configuration Manager.

## <a name="encrypting-credentials"></a>Criptografando credenciais
Para criptografar credenciais no editor de Data Factory, execute as seguintes etapas:

1. Inicie o navegador da Web no **computador do gateway**, navegue até [portal do Azure](https://portal.azure.com). Pesquise seu data factory, se necessário, abra data factory na página **Data Factory** e, em seguida, clique em **autor & implantar** para iniciar o editor de data Factory.
2. Clique em um **serviço vinculado** existente no modo de exibição de árvore para ver sua definição JSON ou crie um serviço vinculado que exija um gateway de gerenciamento de dados (por exemplo: SQL Server ou Oracle).
3. No editor de JSON, para a propriedade **GATEWAYNAME** , insira o nome do gateway.
4. Insira o nome do servidor para a propriedade de **fonte de dados** em **ConnectionString**.
5. Insira o nome do banco de dados para a propriedade de **catálogo inicial** em **ConnectionString**.
6. Clique no botão **criptografar** na barra de comandos que inicia o aplicativo **Gerenciador de credenciais** de clique único. Você deve ver a caixa de diálogo **configurações de credenciais** .

    ![Caixa de diálogo definindo credenciais](./media/data-factory-data-management-gateway/setting-credentials-dialog.png)
7. Na caixa de diálogo **configurações de credenciais** , execute as seguintes etapas:
   1. Selecione a **autenticação** que você deseja que o serviço de data Factory use para se conectar ao banco de dados.
   2. Insira o nome do usuário que tem acesso ao banco de dados para a configuração de **nome** de usuário.
   3. Insira a senha para o usuário para a configuração de **senha** .
   4. Clique em **OK** para criptografar as credenciais e feche a caixa de diálogo.
8. Você deve ver uma propriedade **encryptedCredential** na **ConnectionString** agora.

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
   Se você acessar o portal de um computador diferente do computador do gateway, deverá verificar se o aplicativo Gerenciador de credenciais pode se conectar ao computador do gateway. Se o aplicativo não puder acessar o computador do gateway, ele não permitirá que você defina credenciais para a fonte de dados e teste a conexão com a fonte de dados.

Quando você usa o aplicativo de **credenciais de configuração** , o portal criptografa as credenciais com o certificado especificado na guia **certificado** do **Gateway Configuration Manager** no computador do gateway.

Se você estiver procurando uma abordagem baseada em API para criptografar as credenciais, poderá usar o cmdlet [New-AzDataFactoryEncryptValue](https://docs.microsoft.com/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) do PowerShell para criptografar credenciais. O cmdlet usa o certificado que o gateway está configurado para usar para criptografar as credenciais. Você adiciona credenciais criptografadas ao elemento **EncryptedCredential** de **ConnectionString** no JSON. Você usa o JSON com o cmdlet [New-AzDataFactoryLinkedService](https://docs.microsoft.com/powershell/module/az.datafactory/new-azdatafactorylinkedservice) ou no Editor de data Factory.

```JSON
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```

Há mais uma abordagem para definir credenciais usando o editor de Data Factory. Se você criar um SQL Server serviço vinculado usando o editor e inserir as credenciais em texto sem formatação, as credenciais serão criptografadas usando um certificado que o serviço de Data Factory possui. Ele não usa o certificado que o gateway está configurado para usar. Embora essa abordagem possa ser um pouco mais rápida em alguns casos, ela é menos segura. Portanto, recomendamos que você siga essa abordagem apenas para fins de desenvolvimento/teste.

## <a name="powershell-cmdlets"></a>Cmdlets Powershell
Esta seção descreve como criar e registrar um gateway usando cmdlets Azure PowerShell.

1. Inicie o **Azure PowerShell** no modo de administrador.
2. Faça logon em sua conta do Azure executando o comando a seguir e inserindo suas credenciais do Azure.

    ```powershell
    Connect-AzAccount
    ```
3. Use o cmdlet **New-AzDataFactoryGateway** para criar um gateway lógico da seguinte maneira:

    ```powershell
    $MyDMG = New-AzDataFactoryGateway -Name <gatewayName> -DataFactoryName <dataFactoryName> -ResourceGroupName ADF –Description <desc>
    ```
    **Exemplo de comando e saída**:

    ```
    PS C:\> $MyDMG = New-AzDataFactoryGateway -Name MyGateway -DataFactoryName $df -ResourceGroupName ADF –Description "gateway for walkthrough"

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

1. Em Azure PowerShell, alterne para a pasta: *C:\\\\arquivos de programas\\Microsoft Integration Runtime\\3,0\\PowerShellScript\\* . Execute *RegisterGateway. ps1* associado à variável local **$Key** conforme mostrado no comando a seguir. Esse script registra o agente cliente instalado em seu computador com o gateway lógico que você criar anteriormente.

    ```powershell
    PS C:\> .\RegisterGateway.ps1 $MyDMG.Key
    ```
    ```
    Agent registration is successful!
    ```
    Você pode registrar o gateway em um computador remoto usando o parâmetro IsRegisterOnRemoteMachine. Exemplo:

    ```powershell
    .\RegisterGateway.ps1 $MyDMG.Key -IsRegisterOnRemoteMachine true
    ```
2. Você pode usar o cmdlet **Get-AzDataFactoryGateway** para obter a lista de gateways em seu data Factory. Quando o **status** mostra **online**, significa que o gateway está pronto para uso.

    ```powershell        
    Get-AzDataFactoryGateway -DataFactoryName <dataFactoryName> -ResourceGroupName ADF
    ```
   Você pode remover um gateway usando o cmdlet **Remove-AzDataFactoryGateway** e atualizar a descrição para um gateway usando os cmdlets **set-AzDataFactoryGateway** . Para obter a sintaxe e outros detalhes sobre esses cmdlets, consulte Data Factory referência de cmdlet.  

### <a name="list-gateways-using-powershell"></a>Listar gateways usando o PowerShell

```powershell
Get-AzDataFactoryGateway -DataFactoryName jasoncopyusingstoredprocedure -ResourceGroupName ADF_ResourceGroup
```

### <a name="remove-gateway-using-powershell"></a>Remover gateway usando o PowerShell

```powershell
Remove-AzDataFactoryGateway -Name JasonHDMG_byPSRemote -ResourceGroupName ADF_ResourceGroup -DataFactoryName jasoncopyusingstoredprocedure -Force
```

## <a name="next-steps"></a>Passos seguintes
* Confira o artigo [mover dados entre locais e armazenamentos de dados na nuvem](data-factory-move-data-between-onprem-and-cloud.md) . No passo a passo, você cria um pipeline que usa o gateway para mover dados de um banco de SQL Server local para um blob do Azure.
