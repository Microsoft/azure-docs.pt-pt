---
title: Gateway de gestão de dados para fábrica de dados
description: Utilize o Gateway de Gestão de Dados na Azure Data Factory para mover os seus dados.
author: nabhishek
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 708d84bdb3ebe8fbba6939aa771a9120868d5d1b
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100375209"
---
# <a name="data-management-gateway"></a>Data Management Gateway
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte o [tempo de integração auto-hospedado em](../create-self-hosted-integration-runtime.md).

> [!NOTE]
> Data Management Gateway foi agora renomeado como Self-hosted Integration Runtime.

O Portal de Gestão de Dados é um agente cliente que deve instalar no seu ambiente no local para copiar dados entre lojas de dados em nuvem e no local. As lojas de dados no local suportadas pela Data Factory estão listadas na secção [de fontes de dados suportadas.](data-factory-data-movement-activities.md#supported-data-stores-and-formats)

Este artigo complementa a passagem de dados no Movimento entre o artigo no local e na [nuvem de dados.](data-factory-move-data-between-onprem-and-cloud.md) Na passagem, cria-se um pipeline que utiliza o gateway para mover dados de uma base de dados do SQL Server para uma bolha Azure. Este artigo fornece informações detalhadas sobre a porta de gestão de dados.

Pode escalar uma porta de gestão de dados associando várias máquinas no local com o gateway. Pode aumentar o número de trabalhos de movimento de dados que podem funcionar simultaneamente num nó. Esta funcionalidade também está disponível para um gateway lógico com um único nó. Consulte [a porta de gestão de dados de escala no artigo da Azure Data Factory](data-factory-data-management-gateway-high-availability-scalability.md) para obter mais detalhes.

> [!NOTE]
> Atualmente, gateway suporta apenas a atividade de cópia e atividade de procedimento armazenado na Data Factory. Não é possível utilizar o gateway a partir de uma atividade personalizada para aceder a fontes de dados no local.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Descrição Geral
### <a name="capabilities-of-data-management-gateway"></a>Capacidades de gateway de gestão de dados
Gateway de gestão de dados fornece as seguintes capacidades:

* Modelar fontes de dados no local e fontes de dados em nuvem dentro da mesma fábrica de dados e mover dados.
* Tenha um único painel de vidro para monitorização e gestão com visibilidade para o estado de gateway a partir da página data factory.
* Gerir o acesso a fontes de dados no local de forma segura.
  * Não são necessárias alterações no firewall corporativo. Gateway só faz ligações baseadas em HTTP para abrir internet.
  * Criptografe credenciais para as suas lojas de dados no local com o seu certificado.
* Mover dados de forma eficiente - os dados são transferidos em paralelo, resilientes a problemas de rede intermitentes com lógica de relíndi automática.

### <a name="command-flow-and-data-flow"></a>Fluxo de comando e fluxo de dados
Quando utiliza uma atividade de cópia para copiar dados entre as instalações e a nuvem, a atividade utiliza uma porta de entrada para transferir dados de fonte de dados no local para cloud e vice-versa.

Aqui está o fluxo de dados de alto nível e resumo de passos para cópia com gateway de dados: ![ Fluxo de dados usando gateway](./media/data-factory-data-management-gateway/data-flow-using-gateway.png)

1. O desenvolvedor de dados cria uma porta de entrada para uma Fábrica de Dados Azure utilizando o [portal Azure](https://portal.azure.com) ou [o Comandante PowerShell](/powershell/module/az.datafactory/).
2. O desenvolvedor de dados cria um serviço ligado para uma loja de dados no local especificando o gateway. Como parte da criação do serviço ligado, o desenvolvedor de dados utiliza a aplicação 'Credenciais de Definição' para especificar tipos e credenciais de autenticação. O diálogo de aplicação De Definição de Credenciais comunica com a loja de dados para testar a ligação e o gateway para guardar credenciais.
3. Gateway encripta as credenciais com o certificado associado ao gateway (fornecido pelo desenvolvedor de dados), antes de guardar as credenciais na nuvem.
4. O serviço Data Factory comunica com a porta de entrada para agendar & gestão de postos de trabalho através de um canal de controlo que utiliza uma fila de autocarros partilhada do serviço Azure. Quando um trabalho de atividade de cópia precisa de ser iniciado, a Data Factory faz fila com informações de credenciais. Gateway dá o pontapé de saída depois de votar na fila.
5. O gateway desencripta as credenciais com o mesmo certificado e, em seguida, conecta-se à loja de dados no local com o tipo de autenticação e credenciais adequados.
6. O gateway copia dados de uma loja no local para um armazenamento em nuvem, ou vice-versa dependendo da configuração da Atividade de Cópia no pipeline de dados. Para este passo, o gateway comunica diretamente com serviços de armazenamento baseados na nuvem, como o Azure Blob Storage, através de um canal seguro (HTTPS).

### <a name="considerations-for-using-gateway"></a>Considerações para a utilização do gateway
* Uma única instância de gateway de gestão de dados pode ser usada para várias fontes de dados no local. No entanto, **uma única instância de gateway está ligada apenas a uma fábrica de dados Azure** e não pode ser partilhada com outra fábrica de dados.
* Só pode ter **um único portal de gestão de dados** instalado numa única máquina. Suponha que tem duas fábricas de dados que precisam de aceder a fontes de dados no local, precisa de instalar gateways em dois computadores no local. Por outras palavras, uma porta de entrada está ligada a uma fábrica de dados específica
* O **gateway não precisa de estar na mesma máquina que a fonte de dados**. No entanto, ter o gateway mais próximo da fonte de dados reduz o tempo para o gateway ligar à fonte de dados. Recomendamos que instale o gateway numa máquina diferente da que acolhe fonte de dados no local. Quando o portal e a fonte de dados estão em diferentes máquinas, o gateway não compete por recursos com fonte de dados.
* Pode ter **vários gateways em diferentes máquinas que se ligam à mesma fonte de dados no local.** Por exemplo, pode ter duas portas de entrada que servem duas fábricas de dados, mas a mesma fonte de dados no local está registada em ambas as fábricas de dados.
* Se já tiver um gateway instalado no seu computador a servir um cenário **Power BI,** instale um **gateway separado para a Azure Data Factory** noutra máquina.
* Gateway deve ser utilizado mesmo quando utilizar **o ExpressRoute**.
* Trate a sua fonte de dados como uma fonte de dados no local (que está por trás de uma firewall) mesmo quando utiliza **o ExpressRoute**. Utilize o portal para estabelecer conectividade entre o serviço e a fonte de dados.
* Deve **utilizar o gateway** mesmo que a loja de dados esteja na nuvem num **Azure IaaS VM**.

## <a name="installation"></a>Instalação
### <a name="prerequisites"></a>Pré-requisitos
* As versões **suportadas do Sistema Operativo** são Windows 7, Windows 8/8.1, Windows 10, Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2. A instalação do gateway de gestão de dados num controlador de domínio não é suportada atualmente.
* É necessário .Net Framework 4.5.1 ou superior. Se estiver a instalar o gateway numa máquina do Windows 7, instale o Quadro .NET 4.5 ou mais tarde. Consulte [os requisitos do sistema-quadro .NET](/dotnet/framework/get-started/system-requirements) para obter mais informações.
* A **configuração** recomendada para a máquina de gateway é de pelo menos 2 GHz, 4 núcleos, 8 GB de RAM e disco de 80 GB.
* Se a máquina hospedeira hibernar, o portal não responde aos pedidos de dados. Por isso, configufique um **plano de alimentação** apropriado no computador antes de instalar o gateway. Se a máquina estiver configurada para hibernar, a instalação gateway solicita uma mensagem.
* Tem de ser um administrador na máquina para instalar e configurar com sucesso o gateway de gestão de dados. Pode adicionar utilizadores adicionais à porta de entrada de **dados Do** grupo Windows local. Os membros deste grupo são capazes de usar a ferramenta **Data Management Gateway Configuration Manager** para configurar o gateway.

À medida que a atividade da cópia é executado numa frequência específica, a utilização do recurso (CPU, memória) na máquina também segue o mesmo padrão com tempos de pico e marcha lenta. A utilização de recursos também depende fortemente da quantidade de dados que estão a ser movidos. Quando vários trabalhos de cópia estão em andamento, você vê o uso de recursos subir em tempos de pico.

### <a name="installation-options"></a>Opções de instalação
Gateway de gestão de dados pode ser instalado das seguintes formas:

* Ao descarregar um pacote de configuração MSI do [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=39717). O MSI também pode ser usado para atualizar a porta de entrada de gestão de dados existente para a versão mais recente, com todas as configurações preservadas.
* Clicando em Descarregar e instalar o link **de porta de entrada de dados** sob configuração manual ou **instale diretamente neste computador** sob configuração expressa. Consulte [os dados de movimento entre as instalações e](data-factory-move-data-between-onprem-and-cloud.md) o artigo em nuvem para obter instruções passo a passo sobre a utilização da configuração expressa. O passo manual leva-o ao centro de descarregamento. As instruções para descarregar e instalar o gateway do centro de descarregamento estão na secção seguinte.

### <a name="installation-best-practices"></a>Instalação das melhores práticas:
1. Configure o plano de alimentação na máquina hospedeira para o gateway para que a máquina não se hiberna. Se a máquina hospedeira hibernar, o portal não responde aos pedidos de dados.
2. Apoie o certificado associado ao portal.

### <a name="install-the-gateway-from-download-center"></a>Instale o gateway a partir do centro de descarregamento
1. Navegue para a [página de descarregamento do Microsoft Data Management Gateway](https://www.microsoft.com/download/details.aspx?id=39717).
2. Clique **em Baixar,** selecione a versão **de 64 bits** (32 bits já não está suportado) e clique em **Seguinte**.
3. Executar o **MSI** diretamente ou guardá-lo para o seu disco rígido e executar.
4. Na página **Welcome,** selecione um **idioma** clique **em Seguinte**.
5. **Aceite** o Contrato de Licença End-User e clique em **Seguinte**.
6. Selecione **a pasta** para instalar o gateway e clique em **Seguinte**.
7. Na página **Pronto para instalar,** clique em **Instalar**.
8. Clique **em Terminar** para concluir a instalação.
9. Pegue a chave do portal Azure. Consulte a secção seguinte para obter instruções passo a passo.
10. Na página de **gateway do Gestor** de Configuração do Gateway de Gestão de **Dados** em execução na sua máquina, faça os seguintes passos:
    1. Cole a chave no texto.
    2. Opcionalmente, clique na **tecla 'Mostrar gateway'** para ver o texto da chave.
    3. Clique em **Registar**.

### <a name="register-gateway-using-key"></a>Registar gateway usando chave
#### <a name="if-you-havent-already-created-a-logical-gateway-in-the-portal"></a>Se ainda não criou uma porta de entrada lógica no portal
Para criar uma porta de entrada no portal e obter a chave da página **Configure,** Siga os passos da passagem nos dados do [Movimento entre as instalações e](data-factory-move-data-between-onprem-and-cloud.md) o artigo em nuvem.

#### <a name="if-you-have-already-created-the-logical-gateway-in-the-portal"></a>Se já criou a porta de entrada lógica no portal
1. No portal Azure, navegue na página **data factory** e clique em **azulejos linked Services.**

    ![Página da Fábrica de Dados](media/data-factory-data-management-gateway/data-factory-blade.png)
2. Na página **Serviços Linked,** selecione a **porta de entrada** lógica que criou no portal.

    ![gateway lógico](media/data-factory-data-management-gateway/data-factory-select-gateway.png)
3. Na página **Data Gateway,** clique em Baixar e instalar o **portal de dados**.

    ![Link de descarregamento no portal](media/data-factory-data-management-gateway/download-and-install-link-on-portal.png)
4. Na página **Configurar,** clique na **tecla 'Recriar'.** Clique em Sim na mensagem de aviso depois de a ler cuidadosamente.

    ![Recrie o botão chave](media/data-factory-data-management-gateway/recreate-key-button.png)
5. Clique no botão Copiar ao lado da tecla. A chave é copiada para a área de transferência.

    ![Chave de cópia](media/data-factory-data-management-gateway/copy-gateway-key.png)

### <a name="system-tray-icons-notifications"></a>Ícones/notificações de bandeja do sistema
A imagem a seguir mostra alguns dos ícones da bandeja que você vê.

![ícones de bandeja de sistema](./media/data-factory-data-management-gateway/gateway-tray-icons.png)

Se deslocar cursor sobre a mensagem ícone/notificação do tabuleiro do sistema, consulte detalhes sobre o estado da operação gateway/atualização numa janela popup.

### <a name="ports-and-firewall"></a>Portas e firewall
Há duas firewalls que você precisa considerar: **firewall corporativa** em execução no router central da organização, e **firewall Windows** configurado como um daemon na máquina local onde o gateway está instalado.

![firewalls](./media/data-factory-data-management-gateway/firewalls2.png)

Ao nível da firewall corporativa, é necessário configurar os seguintes domínios e portas de saída:

| Nomes de domínio | Portas | Description |
| --- | --- | --- |
| *.servicebus.windows.net |443 |Usado para comunicação com backend do Serviço de Movimento de Dados |
| *.core.windows.net |443 |Utilizado para cópia encenada utilizando Azure Blob (se configurado)|
| *.frontend.clouddatahub.net |443 |Usado para comunicação com backend do Serviço de Movimento de Dados |
| *.servicebus.windows.net |9350-9354, 5671 |Retransmissão de autocarro de serviço opcional sobre TCP usado pelo Copy Wizard |

Ao nível da firewall do Windows, estas portas de saída estão normalmente ativadas. Caso contrário, pode configurar os domínios e portas em conformidade na máquina de gateway.

> [!NOTE]
> 1. Com base na sua fonte/pias, poderá ter de permitir domínios adicionais e portas de saída na sua firewall corporativa/Windows.
> 2. Para algumas bases de dados em nuvem (por exemplo: [Base de dados Azure SQL](../../azure-sql/database/firewall-configure.md), [Azure Data Lake](../../data-lake-store/data-lake-store-secure-data.md#set-ip-address-range-for-data-access), etc.), poderá ter de permitir o endereço IP da máquina Gateway na sua configuração de firewall.
>
>

#### <a name="copy-data-from-a-source-data-store-to-a-sink-data-store"></a>Copiar dados de uma loja de dados de origem para uma loja de dados de pia
Certifique-se de que as regras de firewall estão ativadas corretamente na firewall corporativa, na firewall do Windows na máquina de gateway e na própria loja de dados. A ativação destas regras permite que o portal se conecte tanto à fonte como ao afundamento com sucesso. Ativar as regras para cada loja de dados que esteja envolvida na operação de cópia.

Por exemplo, para copiar de **uma loja de dados no local para um lavatório Azure SQL Database ou um lavatório Azure Synapse Analytics,** faça os seguintes passos:

* Permitir a comunicação **TCP** de saída na porta **1433** para firewall Windows e firewall corporativo.
* Configure as definições de firewall do servidor SQL lógico para adicionar o endereço IP da máquina de gateway à lista de endereços IP permitidos.

> [!NOTE]
> Se a sua firewall não permitir a porta de saída 1433, gateway não pode aceder diretamente ao Azure SQL. Neste caso, pode utilizar [a Cópia Encenada](./data-factory-copy-activity-performance.md#staged-copy) para a Base de Dados SQL / SQL Gestd Instance / SQL Azure DW. Neste cenário, bastaria o HTTPS (porta 443) para o movimento de dados.
>
>

### <a name="proxy-server-considerations"></a>Considerações do servidor proxy
Se o seu ambiente de rede corporativa utilizar um servidor proxy para aceder à internet, configure o gateway de gestão de dados para utilizar as definições de procuração apropriadas. Pode definir o representante durante a fase inicial de registo.

![Configurar procuração durante o registo](media/data-factory-data-management-gateway/SetProxyDuringRegistration.png)

Gateway utiliza o servidor proxy para se ligar ao serviço de nuvem. Clique **em Alterar** o link durante a configuração inicial. Vê o diálogo **de configuração do representante.**

![Configurar proxy usando config manager 1](media/data-factory-data-management-gateway/SetProxySettings.png)

Existem três opções de configuração:

* **Não utilize procuração**: Gateway não utiliza explicitamente qualquer procuração para se ligar a serviços na nuvem.
* **Utilização do sistema proxy**: Gateway utiliza a definição de procuração configurada em diahost.exe.config e diawp.exe.config. Se nenhum representante estiver configurado em diahost.exe.config e diawp.exe.config, o gateway conecta-se diretamente ao serviço na nuvem sem passar por procuração.
* **Utilize procuração personalizada**: Configure a definição de procuração HTTP para usar para gateway, em vez de utilizar configurações em diahost.exe.config e diawp.exe.config. Endereço e Porto são necessários. O Nome de Utilizador e a Palavra-Passe são opcionais dependendo da definição de autenticação do seu representante. Todas as definições são encriptadas com o certificado de credencial do gateway e armazenadas localmente na máquina de anfitrião gateway.

O serviço de anfitrião da porta de gestão de dados reinicia automaticamente depois de guardar as definições de procuração atualizadas.

Depois de o Gateway ter sido registado com sucesso, se pretender visualizar ou atualizar as definições de procuração, utilize o Gestor de Configurações gateway de gestão de dados.

1. Gestor **de configuração gateway de gestão de dados de lançamento**.
2. Mudar para o separador **Definições**.
3. Clique em **Alterar** o link na secção **HTTP Proxy** para lançar o diálogo **de procuração HTTP.**
4. Depois de clicar no botão **Seguinte,** vê um diálogo de aviso a pedir a sua permissão para guardar a definição de procuração e reiniciar o Serviço de Anfitriões Gateway.

Pode visualizar e atualizar o proxy HTTP utilizando a ferramenta 'Gestor de Configuração'.

![Configurar proxy usando config manager 2](media/data-factory-data-management-gateway/SetProxyConfigManager.png)

> [!NOTE]
> Se configurar um servidor proxy com autenticação NTLM, o Gateway Host Service funciona sob a conta de domínio. Se alterar mais tarde a palavra-passe para a conta de domínio, lembre-se de atualizar as definições de configuração do serviço e reiniciá-la em conformidade. Devido a este requisito, sugerimos que utilize uma conta de domínio dedicada para aceder ao servidor proxy que não requer que atualize a palavra-passe com frequência.
>
>

### <a name="configure-proxy-server-settings"></a>Configurar configurações de servidor de procuração
Se selecionar **A definição de procuração** do sistema para o proxy HTTP, o Gateway utiliza a definição de procuração em diahost.exe.config e diawp.exe.config. Se nenhum representante for especificado em diahost.exe.config e diawp.exe.config, o gateway conecta-se diretamente ao serviço na nuvem sem passar por procuração. O procedimento seguinte fornece instruções para atualizar o ficheiro diahost.exe.config.

1. No File Explorer, faça uma cópia segura de *C: \\ \\ Ficheiros de Programa \\ Microsoft Data Management Gateway \\ 2.0 \\ Shared \\diahost.exe.config* para fazer cópia de segurança do ficheiro original.
2. Lançar Notepad.exe em execução como administrador e abrir o ficheiro de texto *C: \\ \\ Ficheiros de \\ programa Microsoft Data Management Gateway \\ 2.0 \\ Shared \\diahost.exe.config*. Encontra a etiqueta predefinida para system.net como mostrado no seguinte código:

    ```
    <system.net>
        <defaultProxy useDefaultCredentials="true" />
    </system.net>
    ```

    Em seguida, pode adicionar detalhes do servidor proxy como mostrado no seguinte exemplo:

    ```
    <system.net>
        <defaultProxy enabled="true">
            <proxy bypassonlocal="true" proxyaddress="http://proxy.domain.org:8888/" />
        </defaultProxy>
    </system.net>
    ```

    Propriedades adicionais são permitidas dentro da tag proxy para especificar as definições necessárias como scriptLocation. Consulte o [elemento proxy (Definições de rede)](/dotnet/framework/configure-apps/file-schema/network/proxy-element-network-settings) na sintaxe.

    ```
    <proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>
    ```
3. Guarde o ficheiro de configuração para a localização original e, em seguida, reinicie o serviço Data Management Gateway Host, que recolhe as alterações. Para reiniciar o serviço: utilize os serviços applet do painel de controlo ou do Gestor de Configuração gateway de gestão de **dados** > clique no botão **'Serviço de paragem'** e, em seguida, clique no **Serviço 'Iniciar'.** Se o serviço não arrancar, é provável que tenha sido adicionada uma sintaxe de marca XML incorreta no ficheiro de configuração da aplicação que foi editado.

> [!IMPORTANT]
> Não se esqueça de atualizar tanto diahost.exe.config **como** diawp.exe.config.

Além destes pontos, também precisa de se certificar de que o Microsoft Azure está na lista permitida pela sua empresa. A lista de endereços IP válidos do Microsoft Azure pode ser descarregada a partir do [Microsoft Download Center.](https://www.microsoft.com/download/details.aspx?id=41653)

#### <a name="possible-symptoms-for-firewall-and-proxy-server-related-issues"></a>Possíveis sintomas para problemas relacionados com firewall e servidor de procuração
Se encontrar erros semelhantes aos seguintes, é provável que seja devido a uma configuração inadequada da firewall ou do servidor proxy, que bloqueia a entrada de entrada de ligação à Data Factory para se autenticar. Consulte a secção anterior para garantir que a sua firewall e o servidor proxy estão corretamente configurados.

1. Quando tenta registar o portal, recebe o seguinte erro: "Não registou a tecla gateway. Antes de tentar registar novamente a tecla gateway, confirme que o gateway de gestão de dados está num estado conectado e que o Serviço de Anfitriões gateway de gestão de dados está iniciado."
2. Quando abre o Gestor de Configurações, vê o estado como "Desligado" ou "Ligação". Ao visualizar registos de eventos do Windows, em "Event Viewer" > "Registos de aplicações e serviços" > "Data Management Gateway", vê mensagens de erro como o seguinte erro: `Unable to connect to the remote server`
   `A component of Data Management Gateway has become unresponsive and restarts automatically. Component name: Gateway.`

### <a name="open-port-8050-for-credential-encryption"></a>Porta aberta 8050 para encriptação credencial
A aplicação **'Credenciais de Definição'** utiliza a porta de entrada **8050** para transmitir credenciais ao gateway quando configurar um serviço ligado no local no portal Azure. Durante a configuração do gateway, por defeito, a instalação gateway abre-a na máquina de porta de entrada.

Se estiver a utilizar uma firewall de terceiros, pode abrir manualmente a porta 8050. Se encontrar um problema de firewall durante a configuração do gateway, pode tentar utilizar o seguinte comando para instalar o gateway sem configurar a firewall.

```cmd
msiexec /q /i DataManagementGateway.msi NOFIREWALL=1
```

Se optar por não abrir a porta 8050 na máquina de gateway, utilize mecanismos que não utilizem a aplicação **'Definição de Credenciais'** para configurar credenciais de armazenamento de dados. Por exemplo, pode utilizar [o cmdlet New-AzDataFactoryEncryptValue](/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) PowerShell. Consulte a secção de Definição de Credenciais e Segurança sobre a forma como as credenciais de armazenamento de dados podem ser definidas.

## <a name="update"></a>Atualizar
Por predefinição, o gateway de gestão de dados é automaticamente atualizado quando uma versão mais recente do gateway está disponível. O gateway não é atualizado até que todas as tarefas programadas estejam prontas. Nenhuma outra tarefa é processada pelo gateway até que a operação de atualização esteja concluída. Se a atualização falhar, o gateway é revirado para a versão antiga.

Veja o tempo de atualização programado nos seguintes locais:

* A página de propriedades do gateway no portal Azure.
* Página inicial do Gestor de Configuração gateway de gestão de dados
* Mensagem de notificação de bandeja do sistema.

O separador Home do Gestor de Configuração gateway de gestão de dados apresenta o calendário de atualização e a última vez que o gateway foi instalado/atualizado.

![Agendar atualizações](media/data-factory-data-management-gateway/UpdateSection.png)

Pode instalar a atualização imediatamente ou esperar que o gateway seja atualizado automaticamente na hora programada. Por exemplo, a seguinte imagem mostra-lhe a mensagem de notificação mostrada no Gestor de Configuração gateway juntamente com o botão De atualização que pode clicar para a instalar imediatamente.

![Atualização no Gestor de Configuração DMG](./media/data-factory-data-management-gateway/gateway-auto-update-config-manager.png)

A mensagem de notificação na bandeja do sistema seria como mostrado na seguinte imagem:

![Mensagem de bandeja do sistema](./media/data-factory-data-management-gateway/gateway-auto-update-tray-message.png)

Veja o estado do funcionamento da atualização (manual ou automático) na bandeja do sistema. Quando lançar o Gateway Configuration Manager da próxima vez, vê uma mensagem na barra de notificação de que o gateway foi atualizado juntamente com um link para [o novo tópico](data-factory-gateway-release-notes.md).

### <a name="to-disableenable-auto-update-feature"></a>Para desativar/ativar a funcionalidade de atualização automática
Pode desativar/ativar a funcionalidade de atualização automática, fazendo os seguintes passos:

[Para um único portal de nó]
1. Lançar Windows PowerShell na máquina de gateway.
2. Mudar para a pasta *C: \\ \\ Ficheiros de \\ programação Microsoft Integration Runtime \\ 3.0 \\ PowerShellScript. \\*
3. Executar o seguinte comando para ligar a função de atualização automática OFF (desativar).

    ```powershell
    .\IntegrationRuntimeAutoUpdateToggle.ps1 -off
    ```
4. Para ligá-lo de volta:

    ```powershell
    .\IntegrationRuntimeAutoUpdateToggle.ps1 -on
    ```
   [Para o multi-nódoa altamente disponível e porta de entrada escalável](data-factory-data-management-gateway-high-availability-scalability.md)
1. Lançar Windows PowerShell na máquina de gateway.
2. Mudar para a pasta *C: \\ \\ Ficheiros de \\ programação Microsoft Integration Runtime \\ 3.0 \\ PowerShellScript. \\*
3. Executar o seguinte comando para ligar a função de atualização automática OFF (desativar).

    Para gateway com alta disponibilidade, é necessário um param AuthKey extra.
    ```powershell
    .\IntegrationRuntimeAutoUpdateToggle.ps1 -off -AuthKey <your auth key>
    ```
4. Para ligá-lo de volta:

    ```powershell
    .\IntegrationRuntimeAutoUpdateToggle.ps1 -on -AuthKey <your auth key>
    ```

## <a name="configuration-manager"></a>Configuration Manager
Assim que instalar o gateway, pode lançar o Data Management Gateway Configuration Manager de uma das seguintes formas:

1. Na janela **'Pesquisa',** digite **Data Management Gateway** para aceder a este utilitário.
2. Execute o *ConfigManager.exe* executável na pasta: *C: \\ \\ Ficheiros de programa \\ Microsoft Data Management Gateway \\ 2.0 \\ Compartilhado*.

### <a name="home-page"></a>Página de boas-vindas
A página Inicial permite-lhe fazer as seguintes ações:

* Ver estado do gateway (ligado ao serviço de nuvem, etc.).
* **Registe-se** utilizando uma chave do portal.
* **Pare** e inicie o **serviço data Management Gateway Host** na máquina de gateway.
* **Agendar atualizações** numa hora específica dos dias.
* Veja a data em que o portal foi **atualizado pela última vez.**

### <a name="settings-page"></a>Página de definições
A página Definições permite-lhe fazer as seguintes ações:

* Ver, alterar e **certificado de** exportação usado pelo gateway. Este certificado é utilizado para encriptar credenciais de origem de dados.
* Alterar **a porta HTTPS** para o ponto final. O gateway abre uma porta para definir as credenciais de origem de dados.
* **Estado** do ponto final
* O **certificado SSL** view é utilizado para a comunicação TLS/SSL entre o portal e a porta de entrada para definir credenciais para fontes de dados.

### <a name="remote-access-from-intranet"></a>Acesso remoto a partir de intranet
Esta funcionalidade irá ser ativada no futuro. Nas próximas atualizações (v3.4 ou posterior) permitiremos ativar/desativar qualquer conectividade remota que ocorra hoje utilizando a porta 8050 (ver secção acima) enquanto utiliza a aplicação PowerShell ou Credential Manager para encriptar credenciais.

### <a name="diagnostics-page"></a>Página de diagnóstico
A página de Diagnóstico permite-lhe fazer as seguintes ações:

* Ativar a **visualização** verbose, ver registos no visualizador de eventos e enviar registos para a Microsoft se houver uma falha.
* **Teste de ligação** a uma fonte de dados.

### <a name="help-page"></a>Help page
A página Ajuda apresenta as seguintes informações:

* Breve descrição do portal
* Número da versão
* Links para ajuda online, declaração de privacidade e contrato de licença.

## <a name="monitor-gateway-in-the-portal"></a>Monitor gateway no portal
No portal Azure, pode visualizar uma imagem quase real da utilização de recursos (CPU, memória, rede (in/out), etc.) numa máquina de gateway.

1. No portal Azure, navegue na página inicial para a sua fábrica de dados e clique em **azulejos de serviços Linked.**

    ![Home page da fábrica de dados](./media/data-factory-data-management-gateway/monitor-data-factory-home-page.png)
2. Selecione o **gateway** na página **de serviços Linked.**

    ![Página de serviços ligados](./media/data-factory-data-management-gateway/monitor-linked-services-blade.png)
3. Na página **Gateway,** pode ver a memória e o uso do CPU do gateway.

    ![CPU e utilização da memória do gateway](./media/data-factory-data-management-gateway/gateway-simple-monitoring.png)
4. Ativar **as definições avançadas** para ver mais detalhes, como a utilização da rede.
    
    ![Monitorização avançada do gateway](./media/data-factory-data-management-gateway/gateway-advanced-monitoring.png)

A tabela a seguir apresenta descrições de colunas na lista **de nóis gateway:**

Propriedade de Monitorização | Descrição
:------------------ | :----------
Nome | Nome do portal lógico e nós associados ao portal. Nó é uma máquina Windows no local que tem o gateway instalado nele. Para obter informações sobre ter mais de um nó (até quatro nós) num único gateway lógico, consulte [Data Management Gateway - alta disponibilidade e escalabilidade](data-factory-data-management-gateway-high-availability-scalability.md).
Estado | Estado do portal lógico e os nós de gateway. Exemplo: Online/Offline/Limited/etc. Para obter informações sobre estes estados, consulte a secção [de estado do Gateway.](#gateway-status)
Versão | Mostra a versão do gateway lógico e cada nó de gateway. A versão do gateway lógico é determinada com base na versão da maioria dos nós do grupo. Se existirem nós com versões diferentes na configuração lógica do gateway, apenas os nós com o mesmo número de versão que o gateway lógico funcionam corretamente. Outros encontram-se no modo limitado e precisam de ser atualizados manualmente (apenas no caso de falha de atualização automática).
Memória disponível | Memória disponível num nó de gateway. Este valor é um instantâneo quase em tempo real.
Utilização da CPU | Utilização do CPU de um nó de gateway. Este valor é um instantâneo quase em tempo real.
Rede (In/out) | Utilização da rede de um nó de gateway. Este valor é um instantâneo quase em tempo real.
Empregos Simultâneos (Execução/ Limite) | Número de empregos ou tarefas em cada nó. Este valor é um instantâneo quase em tempo real. O limite significa os empregos máximos simultâneos para cada nó. Este valor é definido com base no tamanho da máquina. Pode aumentar o limite para escalar a execução de emprego simultânea em cenários avançados, onde a CPU/memória/rede está subutilização, mas as atividades estão a cronometrar. Esta capacidade também está disponível com um gateway de nó único (mesmo quando a funcionalidade de escalabilidade e disponibilidade não está ativada).
Função | Existem dois tipos de papéis num gateway multi-nó - Despacho e trabalhador. Todos os nós são trabalhadores, o que significa que todos podem ser usados para executar empregos. Há apenas um nó de despachante, que é usado para retirar tarefas/empregos dos serviços de nuvem e enviá-los para diferentes nós de trabalhadores (incluindo a si mesmo).

Nesta página, vê algumas definições que fazem mais sentido quando há dois ou mais nós (cenário de escala para fora) no gateway. Consulte [data Management Gateway - alta disponibilidade e escalabilidade](data-factory-data-management-gateway-high-availability-scalability.md) para detalhes sobre a criação de um gateway multi-nó.

### <a name="gateway-status"></a>Estado do gateway
A tabela a seguir fornece os possíveis estatutos de um nó de **gateway:**

Estado    | Comentários/Cenários
:------- | :------------------
Online | Nó ligado ao serviço Data Factory.
Offline | O nó está desligado.
Atualizar | O nó está a ser atualizado automaticamente.
Limitado | Devido a problemas de conectividade. Pode ser devido à emissão da porta HTTP 8050, problema de conectividade do autocarro de serviço ou problema de sincronização de credenciais.
Inativa | O nó está numa configuração diferente da configuração de outros nós maioritários.<br/><br/> Um nó pode ser inativo quando não pode ligar-se a outros nós.

A tabela seguinte fornece possíveis estatutos de um **portal lógico**. O estado do gateway depende do estado dos nós de gateway.

Estado | Comentários
:----- | :-------
Registo de Necessidades | Nenhum nó está ainda registado neste portal lógico
Online | Os nóns de gateway estão online
Offline | Nenhum nó no estado online.
Limitado | Nem todos os nós desta porta estão em estado saudável. Este estado é um aviso de que algum nó pode estar em baixo! <br/><br/>Pode ser devido a problema de sincronização de credencial no nó de despachante/trabalhador.

## <a name="scale-up-gateway"></a>Escalar gateway
Pode configurar o número de trabalhos de movimento de **dados simultâneos** que podem funcionar num nó para aumentar a capacidade de mover dados entre as lojas de dados no local e na nuvem.

Quando a memória disponível e a CPU não são bem utilizadas, mas a capacidade de marcha lenta é 0, deve aumentar o número de empregos simultâneos que podem funcionar num nó. Você também pode querer aumentar quando as atividades estão a cronometrar porque o gateway está sobrecarregado. Nas definições avançadas de um nó de gateway, pode aumentar a capacidade máxima para um nó.

## <a name="troubleshooting-gateway-issues"></a>Problemas de porta de entrada de resolução de problemas
Consulte o artigo [de resolução de problemas de gateway](data-factory-troubleshoot-gateway-issues.md) para obter informações/dicas para problemas de resolução de problemas com a utilização do gateway de gestão de dados.

## <a name="move-gateway-from-one-machine-to-another"></a>Mover porta de entrada de uma máquina para outra
Esta secção fornece passos para mover o cliente de uma máquina para outra.

1. No portal, navegue na **página inicial** da Data Factory e clique no azulejo dos **Serviços Linked.**

    ![Ligação de gateways de dados](./media/data-factory-data-management-gateway/DataGatewaysLink.png)
2. Selecione o seu gateway na secção **DATA GATEWAYS** da página **Serviços Linked.**

    ![Página de Serviços Ligados com gateway selecionado](./media/data-factory-data-management-gateway/LinkedServiceBladeWithGateway.png)
3. Na página **data gateway,** clique em Baixar e instalar o **gateway de dados**.

    ![Baixar link gateway](./media/data-factory-data-management-gateway/DownloadGatewayLink.png)
4. Na página **Configurar,** clique em Baixar e instalar o **gateway de dados** e siga as instruções para instalar o portal de dados na máquina.

    ![Página de configuração](./media/data-factory-data-management-gateway/ConfigureBlade.png)
5. Mantenha o **Gestor de Configuração gateway de gestão de dados da Microsoft** aberto.

    ![Configuration Manager](./media/data-factory-data-management-gateway/ConfigurationManager.png)
6. Na página **Configurar** no portal, clique na **tecla 'Recriar'** na barra de comando e clique em **Sim** para a mensagem de aviso. Clique no **botão de cópia** ao lado do texto chave que copia a chave da área de transferência. O portal da máquina antiga deixa de funcionar assim que recriar a chave.

    ![Recriar a chave 2](./media/data-factory-data-management-gateway/RecreateKey.png)
7. Cole a **chave** na caixa de texto na página **'Registar Gateway'** do Gestor de Configuração gateway de **gestão de dados** na sua máquina. (opcional) Clique na caixa **de verificação da chave gateway** para ver o texto da chave.

    ![Chave de cópia e Registo](./media/data-factory-data-management-gateway/CopyKeyAndRegister.png)
8. Clique **em Registar** para registar o gateway com o serviço de nuvem.
9. No **separador Definições,** clique em **Alterar** para selecionar o mesmo certificado que foi utilizado com o gateway antigo, introduzir a **palavra-passe** e clicar em **Terminar**.

   ![Especifique o certificado](./media/data-factory-data-management-gateway/SpecifyCertificate.png)

   Pode exportar um certificado a partir do gateway antigo fazendo os seguintes passos: lançar Data Management Gateway Configuration Manager na máquina antiga, mudar para o **separador Certificado,** clicar no botão **Exportar** e seguir as instruções.
10. Após o registo bem sucedido do gateway, deverá ver o **conjunto de Registos** **para Registrado** e **Estado** definido para **Iniciar** na página inicial do Gestor de Configuração gateway.

## <a name="encrypting-credentials"></a>Credenciais encriptadas
Para encriptar credenciais no Editor de Fábrica de Dados, faça os seguintes passos:

1. Lance o navegador web na **máquina gateway,** navegue para o [portal Azure](https://portal.azure.com). Procure a sua fábrica de dados, se necessário, abra a fábrica de dados na página **DATA FACTORY** e, em seguida, clique em **"Autor & Implementar** para lançar o Data Factory Editor.
2. Clique num **serviço ligado** existente na vista da árvore para ver a sua definição JSON ou criar um serviço ligado que requer um gateway de gestão de dados (por exemplo: SQL Server ou Oracle).
3. No editor da JSON, para a propriedade **gatewayName,** insira o nome do gateway.
4. Introduza o nome do servidor para a propriedade **Data Source** na **ligaçãoDese.**
5. Introduza o nome da base de **dados** para a propriedade do Catálogo Inicial na **ligaçãoStragem**.
6. Clique no botão **De criptografado** na barra de comando que lança a aplicação **"Gestor credencial"** de clique uma vez. Deve ver a caixa de diálogo **de definições de credenciais.**

    ![Definição de diálogo de credenciais](./media/data-factory-data-management-gateway/setting-credentials-dialog.png)
7. Na caixa de diálogo **de definições de credenciais,** faça os seguintes passos:
   1. Selecione **a autenticação** que pretende que o serviço Data Factory utilize para ligar à base de dados.
   2. Introduza o nome do utilizador que tem acesso à base de dados para a definição **USERNAME.**
   3. Introduza a palavra-passe para o utilizador para a **definição PASSWORD.**
   4. Clique **em OK** para encriptar as credenciais e fechar a caixa de diálogo.
8. Você deve ver uma propriedade **criptografadaCredential** na **conexãoString** agora.

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
   Se aceder ao portal a partir de uma máquina diferente da máquina de gateway, deve certificar-se de que a aplicação Do Gestor de Credenciais pode ligar-se à máquina do gateway. Se a aplicação não conseguir chegar à máquina de gateway, não lhe permite definir credenciais para a fonte de dados e testar a ligação à fonte de dados.

Quando utiliza a aplicação **'Credenciais de Definição',** o portal encripta as credenciais com o certificado especificado no **separador Certificado** do Gestor de **Configuração gateway** na máquina gateway.

Se estiver à procura de uma abordagem baseada na API para encriptar as credenciais, pode utilizar o cmdlet [PowerShell new-AzDataFactoryEncryptValue](/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) powerShell para encriptar credenciais. O cmdlet usa o certificado que gateway está configurado para usar para encriptar as credenciais. Adiciona credenciais encriptadas ao elemento **Criptamoscrecial** da **ligaçãoStragem** no JSON. Utilize o JSON com o [cmdlet New-AzDataFactoryLinkedService](/powershell/module/az.datafactory/new-azdatafactorylinkedservice) ou no Editor de Fábrica de Dados.

```JSON
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```

Há mais uma abordagem para definir credenciais usando data factory editor. Se criar um serviço ligado ao SQL Server utilizando o editor e introduzir credenciais em texto simples, as credenciais são encriptadas usando um certificado que o serviço Data Factory possui. NÃO utiliza o certificado que o gateway está configurado para utilizar. Embora esta abordagem possa ser um pouco mais rápida em alguns casos, é menos segura. Por isso, recomendamos que siga esta abordagem apenas para fins de desenvolvimento/teste.

## <a name="powershell-cmdlets"></a>Cmdlets do PowerShell
Esta secção descreve como criar e registar um gateway utilizando cmdlets Azure PowerShell.

1. Lançar **Azure PowerShell** no modo de administrador.
2. Inicie sessão na sua conta Azure executando o seguinte comando e introduzindo as suas credenciais Azure.

    ```powershell
    Connect-AzAccount
    ```
3. Utilize o **cmdlet New-AzDataFactoryGateway** para criar um gateway lógico da seguinte forma:

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

1. No Azure PowerShell, mude para a pasta: *C: \\ \\ Ficheiros de \\ programa Microsoft Integration Runtime \\ 3.0 \\ PowerShellScript \\*. Executar *RegisterGateway.ps1* associados à variável local **$Key** como mostrado no comando seguinte. Este script regista o agente cliente instalado na sua máquina com o gateway lógico que cria anteriormente.

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
2. Pode utilizar o **cmdlet Get-AzDataFactoryGateway** para obter a lista de Gateways na sua fábrica de dados. Quando o **Estado** aparece **online,** significa que o seu portal está pronto a ser utilizado.

    ```powershell        
    Get-AzDataFactoryGateway -DataFactoryName <dataFactoryName> -ResourceGroupName ADF
    ```
   Pode remover um gateway utilizando o **cmdlet Remove-AzDataFactoryGateway** e atualizar a descrição de um gateway utilizando as cmdlets **Set-AzDataFactoryGateway.** Para sintaxe e outros detalhes sobre estes cmdlets, consulte Data Factory Cmdlet Reference.  

### <a name="list-gateways-using-powershell"></a>Lista de portais usando PowerShell

```powershell
Get-AzDataFactoryGateway -DataFactoryName jasoncopyusingstoredprocedure -ResourceGroupName ADF_ResourceGroup
```

### <a name="remove-gateway-using-powershell"></a>Remover gateway usando PowerShell

```powershell
Remove-AzDataFactoryGateway -Name JasonHDMG_byPSRemote -ResourceGroupName ADF_ResourceGroup -DataFactoryName jasoncopyusingstoredprocedure -Force
```

## <a name="next-steps"></a>Passos seguintes
* Consulte [os dados de movimento entre as instalações e o artigo lojas de dados em nuvem.](data-factory-move-data-between-onprem-and-cloud.md) Na passagem, cria-se um pipeline que utiliza o gateway para mover dados de uma base de dados do SQL Server para uma bolha Azure.