---
title: Gateway de Gestão de Dados para Fábrica de Dados
description: Criar uma porta de dados para mover dados entre as instalações e a nuvem. Utilize gateway de gestão de dados na Azure Data Factory para mover os seus dados.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: anandsub
ms.assetid: b9084537-2e1c-4e96-b5bc-0e2044388ffd
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 9d86fa9bfe9c17867b8a30519b79d9ee8c5af363
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78357335"
---
# <a name="data-management-gateway"></a>Data Management Gateway
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte o tempo de [integração auto-hospedado em](../create-self-hosted-integration-runtime.md).

> [!NOTE]
> O Portal de Gestão de Dados foi agora renomeado como Tempo de Integração Auto-hospedado.

O portal de gestão de dados é um agente cliente que deve instalar no seu ambiente no local para copiar dados entre lojas de dados cloud e no local. As lojas de dados no local suportadas pela Data Factory estão listadas na secção de fontes de [dados suportadas.](data-factory-data-movement-activities.md#supported-data-stores-and-formats)

Este artigo complementa a passagem nos dados do [Move entre as instalações e as lojas](data-factory-move-data-between-onprem-and-cloud.md) de dados em nuvem. No walkthrough, cria-se um pipeline que utiliza a porta de entrada para mover dados de uma base de dados do SQL Server no local para uma bolha Azure. Este artigo fornece informações detalhadas e aprofundadas sobre o gateway de gestão de dados.

Pode escalar uma porta de gestão de dados associando várias máquinas no local com o portal. Pode aumentar o número de postos de trabalho no movimento de dados que podem funcionar simultaneamente num nó. Esta funcionalidade também está disponível para um portal lógico com um único nó. Consulte o portal de gestão de [dados de Escalana no](data-factory-data-management-gateway-high-availability-scalability.md) artigo da Azure Data Factory para mais detalhes.

> [!NOTE]
> Atualmente, o gateway suporta apenas a atividade de cópia e a atividade de procedimento armazenado na Fábrica de Dados. Não é possível utilizar o portal a partir de uma atividade personalizada para aceder a fontes de dados no local.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Descrição geral
### <a name="capabilities-of-data-management-gateway"></a>Capacidades de gateway de gestão de dados
Gateway de gestão de dados fornece as seguintes capacidades:

* Modelo de fontes de dados no local e fontes de dados em nuvem dentro da mesma fábrica de dados e mover dados.
* Tenha um único painel de vidro para monitorização e gestão com visibilidade no estado de gateway a partir da página data Factory.
* Gerir o acesso a fontes de dados no local de forma segura.
  * Não são necessárias alterações à firewall corporativa. Gateway apenas faz ligações baseadas em HTTP de saída para a internet aberta.
  * Criptografe credenciais para as suas lojas de dados no local com o seu certificado.
* Mover dados de forma eficiente - os dados são transferidos em problemas paralelos e resilientes a problemas de rede intermitentes com lógica de retry automático.

### <a name="command-flow-and-data-flow"></a>Fluxo de comando e fluxo de dados
Quando utiliza uma atividade de cópia para copiar dados entre as instalações e a nuvem, a atividade utiliza uma porta de entrada para transferir dados da fonte de dados no local para a nuvem e vice-versa.

Aqui está o fluxo de dados de alto nível para e resumo de passos para cópia com gateway de dados: ![Fluxo de dados usando gateway](./media/data-factory-data-management-gateway/data-flow-using-gateway.png)

1. O desenvolvedor de dados cria uma porta de entrada para uma Fábrica de Dados Azure utilizando o [portal Azure](https://portal.azure.com) ou [o PowerShell Cmdlet](https://docs.microsoft.com/powershell/module/az.datafactory/).
2. O desenvolvedor de dados cria um serviço ligado para uma loja de dados no local, especificando o gateway. Como parte da configuração do serviço ligado, o desenvolvedor de dados utiliza a aplicação Definição de Credenciais para especificar tipos e credenciais de autenticação. O diálogo de aplicação Definição de Credenciais comunica com a loja de dados para testar a ligação e o portal para guardar credenciais.
3. Gateway encripta as credenciais com o certificado associado ao gateway (fornecido pelo desenvolvedor de dados), antes de guardar as credenciais na nuvem.
4. O serviço Data Factory comunica com a porta de entrada para agendamento e gestão de postos de trabalho através de um canal de controlo que utiliza uma fila de autocarros de serviço Azure partilhada. Quando um trabalho de copy activity precisa de ser iniciado, data Factory faz fila do pedido juntamente com informações credenciais. Gateway dá o pontapé de saída depois de votar na fila.
5. O gateway desencripta as credenciais com o mesmo certificado e, em seguida, liga-se à loja de dados no local com tipo de autenticação e credenciais adequadas.
6. O gateway copia dados de uma loja no local para um armazenamento em nuvem, ou vice-versa dependendo de como a Atividade de Cópia é configurada no pipeline de dados. Para este passo, o portal comunica diretamente com serviços de armazenamento baseados na nuvem, como o Armazenamento De Blob Azure sobre um canal seguro (HTTPS).

### <a name="considerations-for-using-gateway"></a>Considerações para a utilização de gateway
* Uma única instância de gateway de gestão de dados pode ser usada para múltiplas fontes de dados no local. No entanto, uma única instância de **gateway está ligada a apenas uma fábrica** de dados Azure e não pode ser partilhada com outra fábrica de dados.
* Só pode ter **uma instância de gateway de gestão de dados** instalada numa única máquina. Suponha que tem duas fábricas de dados que precisam de aceder às fontes de dados no local, precisa de instalar gateways em dois computadores no local. Por outras palavras, um portal está ligado a uma fábrica de dados específica
* O **portal não precisa de estar na mesma máquina que a fonte de dados**. No entanto, ter gateway mais próximo da fonte de dados reduz o tempo para a porta de entrada ligar à fonte de dados. Recomendamos que instale o portal numa máquina diferente daquela que acolhe a fonte de dados no local. Quando o portal e a fonte de dados estão em máquinas diferentes, o portal não concorre por recursos com fonte de dados.
* Pode ter **vários gateways em diferentes máquinas que se ligam à mesma fonte de dados no local.** Por exemplo, pode ter dois gateways que servem duas fábricas de dados, mas a mesma fonte de dados no local está registada em ambas as fábricas de dados.
* Se já tiver um portal instalado no seu computador servindo um cenário **power BI,** instale uma porta de entrada separada para a **Azure Data Factory** noutra máquina.
* O gateway deve ser utilizado mesmo quando utiliza o **ExpressRoute**.
* Trate a sua fonte de dados como uma fonte de dados no local (que está por trás de uma firewall) mesmo quando utiliza o **ExpressRoute**. Utilize a porta de entrada para estabelecer a conectividade entre o serviço e a fonte de dados.
* Deve **utilizar o portal** mesmo que o depósito de dados esteja na nuvem num **VM Azure IaaS**.

## <a name="installation"></a>Instalação
### <a name="prerequisites"></a>Pré-requisitos
* As versões **do Sistema Operativo** suportadas são Windows 7, Windows 8/8.1, Windows 10, Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2. A instalação do gateway de gestão de dados num controlador de domínio não é atualmente suportada.
* .NET Quadro 4.5.1 ou superior é necessário. Se estiver a instalar o portal numa máquina do Windows 7, instale .NET Framework 4.5 ou mais tarde. Consulte os requisitos do [sistema de enquadramento .NET](https://msdn.microsoft.com/library/8z6watww.aspx) para obter detalhes.
* A **configuração** recomendada para a máquina de gateway é de pelo menos 2 GHz, 4 núcleos, RAM de 8-GB e disco de 80 GB.
* Se a máquina hospedeira hibernar, o portal não responde aos pedidos de dados. Por isso, configure um plano de **alimentação** adequado no computador antes de instalar o portal. Se a máquina estiver configurada para hibernar, a instalação do portal envia uma mensagem.
* Deve ser administrador na máquina para instalar e configurar com sucesso o gateway de gestão de dados. Pode adicionar utilizadores adicionais ao portal de **gestão** de dados Users grupo local Windows. Os membros deste grupo são capazes de usar a ferramenta gestorde configuração do **Gateway De gestão** de dados para configurar o gateway.

À medida que a atividade de cópia corre numa frequência específica, o uso de recursos (CPU, memória) na máquina também segue o mesmo padrão com tempos de pico e ocioso. Utilização de recursos também depende muito a quantidade de dados a ser movidos. Quando vários trabalhos de cópia estão em andamento, você vê o uso de recursos subir durante os tempos de pico.

### <a name="installation-options"></a>Opções de instalação
O gateway de gestão de dados pode ser instalado das seguintes formas:

* Ao descarregar um pacote de configuração MSI do [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=39717). O MSI também pode ser usado para atualizar o portal de gestão de dados existente para a versão mais recente, com todas as configurações preservadas.
* Ao clicar em **Desmarcar e instalar** o link de gateway de dados em configuração manual ou **instalar diretamente neste computador** sob configuração EXPRESS. Consulte [os dados entre as instalações e o](data-factory-move-data-between-onprem-and-cloud.md) artigo em nuvem para obter instruções passo a passo sobre a utilização da configuração expressa. O passo manual leva-o ao centro de descarregamento. As instruções para descarregar e instalar o portal a partir do centro de descarregamento estão na secção seguinte.

### <a name="installation-best-practices"></a>Instalação boas práticas:
1. Configure o plano de alimentação na máquina hospedeira para o portal para que a máquina não hiberna. Se a máquina hospedeira hibernar, o portal não responde aos pedidos de dados.
2. Volte o certificado associado ao portal.

### <a name="install-the-gateway-from-download-center"></a>Instale o portal a partir do centro de descarregamento
1. Navegue para [a página de descarregamento](https://www.microsoft.com/download/details.aspx?id=39717)do Gateway de Gestão de Dados da Microsoft .
2. Clique em **Baixar,** selecione a versão **de 64 bits** (32 bits já não suporta) e clique **em Next**.
3. Execute o **MSI** diretamente ou guarde-o para o disco rígido e corra.
4. Na página **Welcome,** selecione um **clique em idioma** **Seguinte**.
5. **Aceite** o Contrato de Licença de Utilizador Final e clique em **Next**.
6. Selecione **pasta** para instalar o gateway e clique **em Seguinte**.
7. Na página **Ready to install,** clique em **Instalar**.
8. Clique em **Terminar** para concluir a instalação.
9. Pegue a chave do portal Azure. Consulte a secção seguinte para obter instruções passo a passo.
10. Na página de **gateway do Registo** do Gestor de Configuração de Gateway de **Gestão** de Dados em execução na sua máquina, faça os seguintes passos:
    1. Cola a chave no texto.
    2. Opcionalmente, clique na **tecla 'Gateway' para** ver o texto chave.
    3. Clique no **Registo**.

### <a name="register-gateway-using-key"></a>Registar porta de entrada utilizando a chave
#### <a name="if-you-havent-already-created-a-logical-gateway-in-the-portal"></a>Se ainda não criou uma porta de entrada lógica no portal
Para criar uma porta de entrada no portal e obter a chave a partir da página **Configure,** Siga os passos da passagem nos dados move entre as instalações e o artigo [em nuvem.](data-factory-move-data-between-onprem-and-cloud.md)

#### <a name="if-you-have-already-created-the-logical-gateway-in-the-portal"></a>Se já criou a porta de entrada lógica no portal
1. No portal Azure, navegue para a página **data Factory** e clique em azulejo sinuoso dos **Serviços Ligados.**

    ![Página da Fábrica de Dados](media/data-factory-data-management-gateway/data-factory-blade.png)
2. Na página **de Serviços Linked,** selecione o **portal** lógico que criou.

    ![porta de entrada lógica](media/data-factory-data-management-gateway/data-factory-select-gateway.png)
3. Na página **Data Gateway,** clique em **Baixar e instalar**gateway de dados .

    ![Descarregue o link no portal](media/data-factory-data-management-gateway/download-and-install-link-on-portal.png)
4. Na página **Configurar,** clique na **tecla Recriar**. Clique sim na mensagem de aviso depois de lê-la atentamente.

    ![Recriar a chave](media/data-factory-data-management-gateway/recreate-key-button.png)
5. Clique no botão Copiar ao lado da tecla. A chave é copiada para a pasta.

    ![Chave de cópia](media/data-factory-data-management-gateway/copy-gateway-key.png)

### <a name="system-tray-icons-notifications"></a>Ícones/notificações de bandeja do sistema
A imagem que se segue mostra alguns dos ícones da bandeja que vê.

![ícones de bandeja de sistema](./media/data-factory-data-management-gateway/gateway-tray-icons.png)

Se mover o cursor sobre o ícone/mensagem de notificação do sistema, consulte detalhes sobre o estado da operação gateway/atualização numa janela popup.

### <a name="ports-and-firewall"></a>Portas e firewall
Existem duas firewalls que você precisa considerar: **firewall corporativo** funcionando no router central da organização, e **firewall Windows** configurado como um daemon na máquina local onde o gateway está instalado.

![firewalls](./media/data-factory-data-management-gateway/firewalls2.png)

Ao nível da firewall corporativa, é necessário configurar os seguintes domínios e portas de saída:

| Nomes de domínio | Portas | Descrição |
| --- | --- | --- |
| *.servicebus.windows.net |443 |Usado para comunicação com backend do Serviço de Movimento de Dados |
| *.core.windows.net |443 |Utilizado para cópia encenada utilizando O Blob Azure (se configurado)|
| *.frontend.clouddatahub.net |443 |Usado para comunicação com backend do Serviço de Movimento de Dados |
| *.servicebus.windows.net |9350-9354, 5671 |Retransmissão opcional de ônibus de serviço sobre TCP usado pelo Assistente de Cópia |

Ao nível da firewall do Windows, estas portas de saída estão normalmente ativadas. Caso contrário, pode configurar os domínios e portas em conformidade na máquina gateway.

> [!NOTE]
> 1. Com base na sua fonte/pias, poderá ter de whitelist de domínios adicionais e portas de saída na sua firewall corporativa/Windows.
> 2. Para algumas bases de dados cloud (por exemplo: Base de [Dados Azure SQL,](https://docs.microsoft.com/azure/sql-database/sql-database-configure-firewall-settings)Lago de [Dados Azure](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-secure-data#set-ip-address-range-for-data-access), etc.), poderá ser necessário um endereço IP whitelist da máquina Gateway na sua configuração de firewall.
>
>

#### <a name="copy-data-from-a-source-data-store-to-a-sink-data-store"></a>Copiar dados de uma loja de dados de origem para uma loja de dados afundada
Certifique-se de que as regras de firewall estão corretamente ativadas na firewall corporativa, firewall do Windows na máquina gateway e na própria loja de dados. Permitir estas regras permite que a porta de entrada se ligue tanto à fonte como afunde com sucesso. Ativar as regras para cada loja de dados que esteja envolvida na operação de cópia.

Por exemplo, para copiar de **uma loja de dados no local para um lavatório de base de dados Azure SQL ou um depósito de dados Azure SQL,** faça os seguintes passos:

* Permita a comunicação **tCP** de saída na porta **1433** para firewall windows e firewall corporativo.
* Configure as definições de firewall do servidor Azure SQL para adicionar o endereço IP da máquina gateway à lista de endereços IP permitidos.

> [!NOTE]
> Se a sua firewall não permitir a porta de saída 1433, o Gateway não pode aceder diretamente ao Azure SQL. Neste caso, pode utilizar [a Cópia Encenada](https://docs.microsoft.com/azure/data-factory/data-factory-copy-activity-performance#staged-copy) para a Base de Dados SQL Azure/ SQL Azure DW. Neste cenário, bastaria HTTPS (porta 443) para o movimento de dados.
>
>

### <a name="proxy-server-considerations"></a>Considerações de servidor proxy
Se o seu ambiente de rede corporativa utilizar um servidor proxy para aceder à internet, configure o portal de gestão de dados para utilizar as definições de procuração apropriadas. Pode definir o proxy durante a fase inicial de registo.

![Definir procuração durante o registo](media/data-factory-data-management-gateway/SetProxyDuringRegistration.png)

Gateway usa o servidor proxy para ligar ao serviço de nuvem. Clique no link **'Alterar'** durante a configuração inicial. Vê o diálogo de **definição de procuração.**

![Definir proxy usando o gestor de config](media/data-factory-data-management-gateway/SetProxySettings.png)

Existem três opções de configuração:

* **Não utilize procuração**: Gateway não utiliza explicitamente qualquer procuração para se ligar aos serviços de nuvem.
* **Utilização proxy do sistema**: Gateway utiliza a definição de procuração configurada em diahost.exe.config e diawp.exe.config. Se nenhum proxy estiver configurado em diahost.exe.config e diawp.exe.config, o gateway liga-se diretamente ao serviço de nuvem sem passar por procuração.
* **Utilize proxy personalizado**: Configure a definição de proxy HTTP para utilizar para gateway, em vez de utilizar configurações em diahost.exe.config e diawp.exe.config. Endereço e Porto são necessários. O Nome do Utilizador e a Palavra-Passe são opcionais, dependendo da definição de autenticação do seu representante. Todas as definições são encriptadas com o certificado de credencial do gateway e armazenadas localmente na máquina anfitriã do portal.

O serviço de hospedaria de gateway de gestão de dados reinicia automaticamente depois de guardar as definições de procuração atualizadas.

Depois de o gateway ter sido registado com sucesso, se pretender visualizar ou atualizar as definições de proxy, utilize o Gestor de Configuração de Gateway de Gestão de Dados.

1. Lance **Gestor de configuração**de gateway de gestão de dados.
2. Mudar para o separador **Definições**.
3. Clique em **alterar** o link na secção **HTTP Proxy** para lançar o diálogo set **HTTP Proxy.**
4. Depois de clicar no botão **Seguinte,** consulte um diálogo de aviso pedindo a sua permissão para salvar a definição de procuração e reiniciar o Serviço de Hospedaria gateway.

Pode visualizar e atualizar o proxy HTTP utilizando a ferramenta 'Gestor de Configuração'.

![Definir proxy usando o gestor de config](media/data-factory-data-management-gateway/SetProxyConfigManager.png)

> [!NOTE]
> Se configurar um servidor proxy com autenticação NTLM, o Gateway Host Service passa por baixo da conta de domínio. Se alterar a palavra-passe para a conta de domínio mais tarde, lembre-se de atualizar as definições de configuração do serviço e reiniciá-la em conformidade. Devido a este requisito, sugerimos que utilize uma conta de domínio dedicada para aceder ao servidor proxy que não o exija atualizar a palavra-passe com frequência.
>
>

### <a name="configure-proxy-server-settings"></a>Configurar as definições do servidor proxy
Se selecionar a definição de proxy do **sistema utilize** para o proxy HTTP, o gateway utiliza a definição de procuração em diahost.exe.config e diawp.exe.config. Se não for especificado qualquer procuração em diahost.exe.config e diawp.exe.config, o gateway liga-se diretamente ao serviço de nuvem sem passar por procuração. O procedimento seguinte fornece instruções para atualizar o ficheiro diahost.exe.config.

1. No File Explorer, faça uma cópia segura de *C:\\\\Ficheiros do Programa\\Microsoft Data Management Gateway\\2.0\\partilhou\\diahost.exe.config* para fazer o backing do ficheiro original.
2. Lançamento Notepad.exe funcionando como administrador e ficheiro de texto aberto *C:\\\\Ficheiros de programas\\Microsoft Data Management Gateway\\2.0\\partilhou\\diahost.exe.config*. Encontra a etiqueta predefinida para system.net, conforme mostrado no seguinte código:

    ```
    <system.net>
        <defaultProxy useDefaultCredentials="true" />
    </system.net>
    ```

    Pode então adicionar detalhes do servidor proxy, como mostrado no seguinte exemplo:

    ```
    <system.net>
        <defaultProxy enabled="true">
            <proxy bypassonlocal="true" proxyaddress="http://proxy.domain.org:8888/" />
        </defaultProxy>
    </system.net>
    ```

    Propriedades adicionais são permitidas dentro da etiqueta proxy para especificar as definições necessárias como scriptLocalização. Consulte o [Proxy Element (Definições de Rede)](https://msdn.microsoft.com/library/sa91de1e.aspx) na sintaxe.

    ```
    <proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>
    ```
3. Guarde o ficheiro de configuração para a localização original e, em seguida, reinicie o serviço de hospedaria gateway de gestão de dados, que recolhe as alterações. Para reiniciar o serviço: utilize os serviços applet do painel de controlo, ou do Gestor de **Configuração** de Gateway de Gestão de Dados > clique no botão **Stop Service** e, em seguida, clique no Serviço de **Arranque**. Se o serviço não arrancar, é provável que tenha sido adicionado um sintaxe de etiqueta XML incorreto no ficheiro de configuração da aplicação que foi editado.

> [!IMPORTANT]
> Não se esqueça de atualizar **tanto o** diahost.exe.config e o diawp.exe.config.

Além destes pontos, também precisa de se certificar de que o Microsoft Azure está na lista branca da sua empresa. A lista de endereços IP válidos do Microsoft Azure pode ser descarregada a partir do [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=41653).

#### <a name="possible-symptoms-for-firewall-and-proxy-server-related-issues"></a>Possíveis sintomas para problemas relacionados com firewall e proxy servidor
Se encontrar erros semelhantes aos seguintes, é provável que seja devido a uma configuração inadequada da firewall ou do servidor proxy, que bloqueia a porta de entrada da ligação à Data Factory para se autenticar. Consulte a secção anterior para garantir que o seu servidor de firewall e proxy está corretamente configurado.

1. Quando tenta registar o gateway, recebe o seguinte erro: "Não registou a chave de gateway. Antes de tentar registar novamente a chave de gateway, confirme que o portal de gestão de dados está em estado conectado e o Serviço de Hospedaria gateway de gestão de dados está iniciado."
2. Ao abrir o Gestor de Configuração, vê o estado como "Desligado" ou "Connecting". Ao visualizar registos de eventos do Windows, em "Event Viewer" > "Registos de Aplicações e Serviços" > "Data Management Gateway", vê mensagens de erro como o seguinte erro: `Unable to connect to the remote server`
   `A component of Data Management Gateway has become unresponsive and restarts automatically. Component name: Gateway.`

### <a name="open-port-8050-for-credential-encryption"></a>Porta aberta 8050 para encriptação credencial
A aplicação **Definição** de Credenciais utiliza a porta de entrada **8050** para retransmitir credenciais para o portal Gateway quando configura um serviço ligado ao local no portal Azure. Durante a configuração do gateway, por padrão, a instalação do gateway abre-a na máquina de gateway.

Se estiver a utilizar uma firewall de terceiros, pode abrir manualmente a porta 8050. Se encontrar problemas de firewall durante a configuração do gateway, pode tentar utilizar o seguinte comando para instalar o gateway sem configurar a firewall.

    msiexec /q /i DataManagementGateway.msi NOFIREWALL=1

Se optar por não abrir a porta 8050 na máquina gateway, utilize mecanismos que não sejam a aplicação Definição de **Credenciais** para configurar as credenciais de loja de dados. Por exemplo, pode utilizar [o cmdlet New-AzDataFactoryEncryptValuePowerShell.](https://docs.microsoft.com/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) Consulte a definição de credenciais e secção de segurança sobre como as credenciais de loja de dados podem ser definidas.

## <a name="update"></a>Atualizar
Por padrão, o gateway de gestão de dados é automaticamente atualizado quando uma versão mais recente do gateway está disponível. O portal não é atualizado até que todas as tarefas programadas estejam prontas. Nenhuma outra tarefa é processada pelo portal até que a operação de atualização esteja concluída. Se a atualização falhar, o gateway é revirado para a versão antiga.

Veja o tempo de atualização programado nos seguintes locais:

* A página de propriedades gateway no portal Azure.
* Página inicial do Gestor de Configuração de Gateway de Gestão de Dados
* Mensagem de notificação da bandeja do sistema.

O separador Home do Gestor de Configuração de Gateway de Gestão de Dados apresenta o calendário da atualização e a última vez que o gateway foi instalado/atualizado.

![Agendar atualizações](media/data-factory-data-management-gateway/UpdateSection.png)

Pode instalar a atualização imediatamente ou esperar que o portal seja atualizado automaticamente na hora programada. Por exemplo, a imagem que se segue mostra-lhe a mensagem de notificação mostrada no Gateway Configuration Manager juntamente com o botão 'Actualizar' que pode clicar para a instalar imediatamente.

![Atualização no Gestor de Configuração DMG](./media/data-factory-data-management-gateway/gateway-auto-update-config-manager.png)

A mensagem de notificação na bandeja do sistema seria como mostrado na seguinte imagem:

![Mensagem bandeja do sistema](./media/data-factory-data-management-gateway/gateway-auto-update-tray-message.png)

Vê o estado do funcionamento da atualização (manual ou automático) na bandeja do sistema. Quando lançar o Gateway Configuration Manager da próxima vez, vê uma mensagem na barra de notificação de que o portal foi atualizado juntamente com um link para [o novo tópico](data-factory-gateway-release-notes.md).

### <a name="to-disableenable-auto-update-feature"></a>Para desativar/ativar a funcionalidade de atualização automática
Pode desativar/ativar a funcionalidade de atualização automática fazendo os seguintes passos:

[Para gateway de nó único]
1. Lance o Windows PowerShell na máquina de porta de entrada.
2. Mude para o C:\\\\Ficheiros do programa\\prazo de integração da *Microsoft\\3.0\\a pasta\\PowerShellScript.*
3. Executar o comando seguinte para desligar a função de atualização automática OFF (desativar).

    ```powershell
    .\IntegrationRuntimeAutoUpdateToggle.ps1 -off
    ```
4. Para voltar a ligá-lo:

    ```powershell
    .\IntegrationRuntimeAutoUpdateToggle.ps1 -on
    ```
   [Para porta de entrada multi-nó altamente disponível e escalável](data-factory-data-management-gateway-high-availability-scalability.md)
1. Lance o Windows PowerShell na máquina de porta de entrada.
2. Mude para o C:\\\\Ficheiros do programa\\prazo de integração da *Microsoft\\3.0\\a pasta\\PowerShellScript.*
3. Executar o comando seguinte para desligar a função de atualização automática OFF (desativar).

    Para gateway com funcionalidade de alta disponibilidade, é necessário um param AuthKey extra.
    ```powershell
    .\IntegrationRuntimeAutoUpdateToggle.ps1 -off -AuthKey <your auth key>
    ```
4. Para voltar a ligá-lo:

    ```powershell
    .\IntegrationRuntimeAutoUpdateToggle.ps1 -on -AuthKey <your auth key>
    ```

## <a name="configuration-manager"></a>Configuration Manager
Assim que instalar o gateway, pode lançar o Gestor de Configuração gateway de gestão de dados de uma das seguintes formas:

1. Na janela **de pesquisa,** digite gateway de **gestão** de dados para aceder a este utilitário.
2. Execute o *Executable ConfigManager.exe* na pasta: *C:\\\\Program Files\\Microsoft Data Management Gateway\\2.0\\Shared*.

### <a name="home-page"></a>Página de boas-vindas
A página Inicial permite-lhe fazer as seguintes ações:

* Ver estado do portal (ligado ao serviço de nuvem, etc.).
* **Registe-se** com uma chave do portal.
* **Pare** e inicie o serviço de **hospedar gateway** de gestão de dados na máquina gateway.
* **Agendar atualizações** numa hora específica dos dias.
* Veja a data em que o portal foi **atualizado pela última vez**.

### <a name="settings-page"></a>Página de definições
A página Definições permite-lhe fazer as seguintes ações:

* Ver, alterar e **certificado** de exportação usado pelo portal. Este certificado é utilizado para encriptar credenciais de origem de dados.
* Alterar **a porta HTTPS** para o ponto final. O portal abre uma porta para definir as credenciais de origem de dados.
* **Estado** do ponto final
* Ver **certificado SSL** é usado para comunicação SSL entre portal e a porta de entrada para definir credenciais para fontes de dados.

### <a name="remote-access-from-intranet"></a>Acesso remoto a partir de intranet
Esta funcionalidade estará ativada no futuro. Nas próximas atualizações (v3.4 ou posteriores) permitiremos ativar/desativar qualquer conectividade remota que hoje aconteça utilizando a porta 8050 (ver secção acima) enquanto utiliza a aplicação PowerShell ou Credential Manager para encriptar credenciais.

### <a name="diagnostics-page"></a>Página de diagnóstico
A página de Diagnóstico permite-lhe fazer as seguintes ações:

* Ativar o **registo**verbose, verter registos no espectador do evento e enviar registos para a Microsoft se houver uma falha.
* **Teste a ligação** a uma fonte de dados.

### <a name="help-page"></a>Página de ajuda
A página Ajuda apresenta as seguintes informações:

* Breve descrição do portal
* Número de versão
* Links para ajuda online, declaração de privacidade e contrato de licença.

## <a name="monitor-gateway-in-the-portal"></a>Monitor gateway no portal
No portal Azure, pode ver instantâneos quase em tempo real de utilização de recursos (CPU, memória, rede (dentro/fora), etc.) numa máquina de gateway.

1. No portal Azure, navegue para a página inicial da sua fábrica de dados e clique em **azulejo de serviços Linked.**

    ![Home page da fábrica de dados](./media/data-factory-data-management-gateway/monitor-data-factory-home-page.png)
2. Selecione o **gateway** na página de **serviços Linked.**

    ![Página de serviços ligados](./media/data-factory-data-management-gateway/monitor-linked-services-blade.png)
3. Na página **Gateway,** pode ver a memória e o uso do CPU do portal.

    ![CPU e utilização da memória do gateway](./media/data-factory-data-management-gateway/gateway-simple-monitoring.png)
4. Ative **as definições avançadas** para ver mais detalhes, como o uso da rede.
    
    ![Monitorização avançada do gateway](./media/data-factory-data-management-gateway/gateway-advanced-monitoring.png)

A tabela seguinte apresenta descrições de colunas na lista gateway **nódosos:**

Propriedade de Monitorização | Descrição
:------------------ | :----------
Nome | Nome da porta de entrada lógica e nódoas associadas à porta de entrada. O nó é uma máquina Windows no local que tem o portal instalado nele. Para obter informações sobre ter mais de um nó (até quatro nós) numa única porta de entrada lógica, consulte Data [Management Gateway - alta disponibilidade e escalabilidade](data-factory-data-management-gateway-high-availability-scalability.md).
Estado | Estado da porta de entrada lógica e dos nódosos de entrada. Exemplo: Online/Offline/Limited/etc. Para obter informações sobre estes estados, consulte a secção [de estado gateway.](#gateway-status)
Versão | Mostra a versão do portal lógico e cada nó de gateway. A versão do portal lógico é determinada com base na versão da maioria dos nódosos do grupo. Se houver nós com versões diferentes na configuração lógica do portal, apenas os nós com o mesmo número de versão que a função lógica do gateway funcionam corretamente. Outros estão no modo limitado e precisam de ser atualizados manualmente (apenas no caso de a atualização automática falhar).
Memória disponível | Memória disponível em um nó de entrada. Este valor é um instantâneo quase em tempo real.
Utilização da CPU | Utilização da CPU de um nó de gateway. Este valor é um instantâneo quase em tempo real.
Networking (In/out) | Utilização da rede de um nó de entrada. Este valor é um instantâneo quase em tempo real.
Empregos simultâneos (execução/ limite) | Número de postos de trabalho ou tarefas em cada nó. Este valor é um instantâneo quase em tempo real. Limite significa o máximo de empregos simultâneos para cada nó. Este valor é definido com base no tamanho da máquina. Pode aumentar o limite para aumentar a execução de emprego simultânea em cenários avançados, onde cpU/memória/rede é subutilizado, mas as atividades estão a cronometrar. Esta capacidade também está disponível com um gateway de um nó único (mesmo quando a funcionalidade de escalabilidade e disponibilidade não está ativada).
Função | Existem dois tipos de funções num portal multi-nó - Despachante e trabalhador. Todos os nós são trabalhadores, o que significa que todos podem ser usados para executar empregos. Existe apenas um nó deexpedidor, que é usado para retirar tarefas/empregos dos serviços de nuvem e despachá-los para diferentes nós de trabalhador (incluindo a si mesmo).

Nesta página, você vê algumas configurações que fazem mais sentido quando há dois ou mais nós (cenário de escala para fora) no gateway. Consulte data [Management Gateway - alta disponibilidade e escalabilidade](data-factory-data-management-gateway-high-availability-scalability.md) para detalhes sobre a criação de um gateway multi-nó.

### <a name="gateway-status"></a>Estado do gateway
O quadro seguinte fornece possíveis estatutos de um nó de **gateway:**

Estado  | Comentários/Cenários
:------- | :------------------
Online | Nó ligado ao serviço data Factory.
Banda | O nó está offline.
Atualizar | O nó está a ser atualizado automaticamente.
Limitado | Devido a um problema de conectividade. Pode ser devido a http porta 8050, problema de conectividade de ônibus de serviço, ou problema de sincronização credencial.
Inativo | O nó está numa configuração diferente da configuração de outros nódosos maioritários.<br/><br/> Um nó pode estar inativo quando não consegue ligar-se a outros nódosos.

A tabela que se segue fornece possíveis estatutos de uma **porta de entrada lógica**. O estado do portal depende dos estados dos nódeos de gateway.

Estado | Comentários
:----- | :-------
Necessidades De Inscrição | Nenhum nó ainda está registado nesta porta de entrada lógica.
Online | Os nódosos gateway estão online
Banda | Não nó em estado on-line.
Limitado | Nem todos os nós nesta porta estão em estado saudável. Este estado é um aviso de que algum nó pode estar em baixo! <br/><br/>Pode ser devido a uma questão de sincronização credencial no nó de expedidor/trabalhador.

## <a name="scale-up-gateway"></a>Escalar o gateway
Pode configurar o número de **trabalhos simultâneos** de movimento de dados que podem funcionar num nó para aumentar a capacidade de movimentar dados entre as lojas de dados no local e as lojas de dados em nuvem.

Quando a memória disponível e a CPU não são bem utilizadas, mas a capacidade inativa é 0, deve aumentar o número de empregos simultâneos que podem funcionar num nó. Você também pode querer escalar quando as atividades estão cronometradas porque o gateway está sobrecarregado. Nas configurações avançadas de um nó de gateway, pode aumentar a capacidade máxima para um nó.

## <a name="troubleshooting-gateway-issues"></a>Problemas de resolução de problemas problemas
Consulte o artigo de problemas de resolução de [gateways](data-factory-troubleshoot-gateway-issues.md) para informações/dicas para problemas de resolução de problemas com a utilização do gateway de gestão de dados.

## <a name="move-gateway-from-one-machine-to-another"></a>Mover porta de entrada de uma máquina para outra
Esta secção fornece passos para mover o cliente gateway de uma máquina para outra máquina.

1. No portal, navegue para a **página inicial da Data Factory**e clique no azulejo dos **Serviços Linked.**

    ![Link gateways de dados](./media/data-factory-data-management-gateway/DataGatewaysLink.png)
2. Selecione o seu portal na secção **DATA GATEWAYS** da página **Serviços Linked.**

    ![Página de Serviços Ligados com gateway selecionado](./media/data-factory-data-management-gateway/LinkedServiceBladeWithGateway.png)
3. Na página de **gateway Data,** clique em **Baixar e instalar gateway de dados**.

    ![Descarregue o link gateway](./media/data-factory-data-management-gateway/DownloadGatewayLink.png)
4. Na página **Configure,** clique em Baixar e instalar o **portal de dados**e siga as instruções para instalar o portal de dados na máquina.

    ![Página de configuração](./media/data-factory-data-management-gateway/ConfigureBlade.png)
5. Mantenha aberto o Gestor de Configuração gateway de gestão de dados da **Microsoft.**

    ![Configuration Manager](./media/data-factory-data-management-gateway/ConfigurationManager.png)
6. Na página **Configure** no portal, clique em **Recriar** a tecla na barra de comando e clique **em Sim** para a mensagem de aviso. Clique no **botão de cópia** ao lado do texto chave que copia a chave para a área de recortes. A porta de entrada da máquina antiga deixa de funcionar assim que recria a chave.

    ![Recriar a chave](./media/data-factory-data-management-gateway/RecreateKey.png)
7. Colhe a **chave** na caixa de texto na página **'Gateway' do Gestor** de Configuração de Gateway de **Gestão** de Dados na sua máquina. (opcional) Clique na caixa de verificação da chave de **gateway para** ver o texto chave.

    ![Chave de cópia e Registo](./media/data-factory-data-management-gateway/CopyKeyAndRegister.png)
8. Clique em **Registar** para registar o portal com o serviço de nuvem.
9. No separador **Definições,** clique em **Alterar** para selecionar o mesmo certificado que foi utilizado com o gateway antigo, introduza a **palavra-passe**, e clique em **Terminar**.

   ![Especificar Certificado](./media/data-factory-data-management-gateway/SpecifyCertificate.png)

   Pode exportar um certificado a partir do antigo portal, fazendo os seguintes passos: lançar Gestor de Configuração gateway de gestão de dados na máquina antiga, mudar para o separador **Certificado,** clicar no botão **Export** e seguir as instruções.
10. Após o registo bem sucedido do gateway, deve ver o conjunto **de registo** **sinuoso** e **de estado** definido para **Iniciar** na página inicial do Gateway Configuration Manager.

## <a name="encrypting-credentials"></a>Credenciais de encriptação
Para encriptar credenciais no Editor da Fábrica de Dados, faça os seguintes passos:

1. Lance o navegador web na máquina de **gateway,** navegue para o [portal Azure.](https://portal.azure.com) Procure a sua fábrica de dados, se necessário, abra a fábrica de dados na página **DATA FACTORY** e, em seguida, clique em **Author & Deploy** para lançar Data Factory Editor.
2. Clique num **serviço ligado** existente na vista da árvore para ver a sua definição JSON ou criar um serviço ligado que requer um gateway de gestão de dados (por exemplo: SQL Server ou Oracle).
3. No editor da JSON, para a propriedade **gatewayName,** insira o nome do gateway.
4. Introduza o nome do servidor para a propriedade **Data Source** na **ligaçãoString**.
5. Introduza o nome da base de dados da propriedade **Inicial Catalog** na **ligaçãoString**.
6. Clique no botão **Encrypt** na barra de comando que lança a aplicação click-once **Credential Manager.** Deve ver a caixa de diálogo definição de **credenciais.**

    ![Definição de diálogo de credenciais](./media/data-factory-data-management-gateway/setting-credentials-dialog.png)
7. Na caixa de diálogo **Definição de credenciais,** faça os seguintes passos:
   1. **Selecione a autenticação** que pretende que o serviço Data Factory utilize para se ligar à base de dados.
   2. Introduza o nome do utilizador que tenha acesso à base de dados para a definição **USERNAME.**
   3. Introduza a palavra-passe para o utilizador para a definição **de PASSWORD.**
   4. Clique **em OK** para encriptar credenciais e fechar a caixa de diálogo.
8. Você deve ver uma propriedade **Credential encriptada** na **conexão String** agora.

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
   Se aceder ao portal a partir de uma máquina diferente da máquina de gateway, deve certificar-se de que a aplicação Credenciais Gestor pode ligar-se à máquina de gateway. Se a aplicação não chegar à máquina de gateway, não lhe permite definir credenciais para a fonte de dados e testar a ligação à fonte de dados.

Quando utiliza a aplicação Definição de **Credenciais,** o portal encripta as credenciais com o certificado especificado no separador **Certificado** do **Gateway Configuration Manager** na máquina gateway.

Se estiver à procura de uma abordagem baseada em API para encriptar as credenciais, pode utilizar o cmdlet [PowerShell New-AzDataFactoryEncryptValue Para](https://docs.microsoft.com/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) encriptar credenciais. O cmdlet utiliza o certificado que o gateway está configurado para ser utilizado para encriptar as credenciais. Adicione credenciais encriptadas ao elemento **EncryptedCredential** da **ligaçãoString** no JSON. Utiliza o JSON com o [cmdlet New-AzDataFactoryLinkedService](https://docs.microsoft.com/powershell/module/az.datafactory/new-azdatafactorylinkedservice) ou no Data Factory Editor.

```JSON
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```

Há mais uma abordagem para definir credenciais usando data factory editor. Se criar um serviço ligado ao SQL Server utilizando o editor e introduzir credenciais em texto simples, as credenciais são encriptadas utilizando um certificado que o serviço Data Factory possui. NÃO utiliza o certificado que o gateway está configurado para usar. Embora esta abordagem possa ser um pouco mais rápida em alguns casos, é menos segura. Por isso, recomendamos que siga esta abordagem apenas para fins de desenvolvimento/teste.

## <a name="powershell-cmdlets"></a>Cmdlets do PowerShell
Esta secção descreve como criar e registar um portal utilizando cmdlets Azure PowerShell.

1. Lançar **o Azure PowerShell** em modo administrador.
2. Faça login na sua conta Azure executando o seguinte comando e inserindo as suas credenciais Azure.

    ```powershell
    Connect-AzAccount
    ```
3. Utilize o cmdlet **New-AzDataFactoryGateway** para criar uma porta de entrada lógica da seguinte forma:

    ```powershell
    $MyDMG = New-AzDataFactoryGateway -Name <gatewayName> -DataFactoryName <dataFactoryName> -ResourceGroupName ADF –Description <desc>
    ```
    **Exemplo de comando e saída:**

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

1. No Azure PowerShell, mude para a pasta: *C:\\\\Ficheiros do programa\\Prazo de Integração da Microsoft\\3.0\\PowerShellScript\\* . Executar *RegisterGateway.ps1* associado à variável local **$Key** como mostrado no comando seguinte. Este script regista o agente cliente instalado na sua máquina com o portal lógico que cria anteriormente.

    ```powershell
    PS C:\> .\RegisterGateway.ps1 $MyDMG.Key
    ```
    ```
    Agent registration is successful!
    ```
    Pode registar o portal numa máquina remota utilizando o parâmetro IsRegisterOnRemoteMachine. Exemplo:

    ```powershell
    .\RegisterGateway.ps1 $MyDMG.Key -IsRegisterOnRemoteMachine true
    ```
2. Pode utilizar o cmdlet **Get-AzDataFactoryGateway** para obter a lista de Gateways na sua fábrica de dados. Quando o **Estado** aparece **on-line,** significa que o seu portal está pronto a ser utilizado.

    ```powershell        
    Get-AzDataFactoryGateway -DataFactoryName <dataFactoryName> -ResourceGroupName ADF
    ```
   Pode remover um portal utilizando o cmdlet **Remove-AzDataFactoryGateway** e atualizar a descrição de um gateway utilizando os cmdlets **Set-AzDataFactoryGateway.** Para sintaxe e outros detalhes sobre estes cmdlets, consulte Data Factory Cmdlet Reference.  

### <a name="list-gateways-using-powershell"></a>Lista de gateways usando PowerShell

```powershell
Get-AzDataFactoryGateway -DataFactoryName jasoncopyusingstoredprocedure -ResourceGroupName ADF_ResourceGroup
```

### <a name="remove-gateway-using-powershell"></a>Remover gateway usando PowerShell

```powershell
Remove-AzDataFactoryGateway -Name JasonHDMG_byPSRemote -ResourceGroupName ADF_ResourceGroup -DataFactoryName jasoncopyusingstoredprocedure -Force
```

## <a name="next-steps"></a>Passos Seguintes
* Consulte [os dados entre as instalações e as lojas de dados em nuvem.](data-factory-move-data-between-onprem-and-cloud.md) No walkthrough, cria-se um pipeline que utiliza a porta de entrada para mover dados de uma base de dados do SQL Server no local para uma bolha Azure.
