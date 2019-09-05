---
title: Instalar gateway de dados local-aplicativos lógicos do Azure | Microsoft Docs
description: Antes de poder acessar dados locais de aplicativos lógicos do Azure, baixe e instale o gateway de dados local
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: arthii, LADocs
ms.topic: article
ms.date: 10/01/2018
ms.openlocfilehash: 36fb40dcee010ab68dc87eb6f81c0b2fb8977914
ms.sourcegitcommit: aebe5a10fa828733bbfb95296d400f4bc579533c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/05/2019
ms.locfileid: "70376383"
---
# <a name="install-on-premises-data-gateway-for-azure-logic-apps"></a>Instalar o gateway de dados local para aplicativos lógicos do Azure

Antes de poder se conectar a fontes de dados locais de aplicativos lógicos do Azure, baixe e instale o gateway de dados local em um computador local. O gateway funciona como uma ponte que fornece transferência rápida de dados e criptografia entre fontes de dados locais (não na nuvem) e seus aplicativos lógicos. Este artigo mostra como você pode baixar, instalar e configurar seu gateway de dados local.

Você pode usar a mesma instalação de gateway com outros serviços, como Power BI, Microsoft Flow, PowerApps e Azure Analysis Services. Saiba mais sobre [como funciona o gateway de dados](#gateway-cloud-service).

<a name="supported-connections"></a>

O gateway dá suporte a [conectores locais](../connectors/apis-list.md#on-premises-connectors) no aplicativo lógico do Azure para estas fontes de dados:

* BizTalk Server 2016
* Sistema de ficheiros
* IBM DB2  
* IBM Informix
* IBM MQ
* MySQL
* Oracle Database
* PostgreSQL
* SAP
* SharePoint Server
* SQL Server
* Teradata

Para obter informações sobre como usar o gateway com outros serviços, consulte estes artigos:

* [Gateway de dados local do Microsoft Power BI](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem/)
* [Microsoft PowerApps gateway de dados local](https://powerapps.microsoft.com/tutorials/gateway-management/)
* [Microsoft Flow gateway de dados local](https://flow.microsoft.com/documentation/gateway-manage/)
* [Azure Analysis Services Gateway de dados local](../analysis-services/analysis-services-gateway.md)

<a name="requirements"></a>

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/).

  Durante a instalação do gateway, você entra nessa conta para que possa associar a instalação do gateway à sua assinatura do Azure. Posteriormente, você também usa a mesma conta ao criar um recurso do Azure para a instalação do gateway no portal do Azure.

* Aqui estão os requisitos para seu computador local:

  **Requisitos mínimos**

  * .NET Framework 4.5.2
  * versão de 64 bits do Windows 7 ou Windows Server 2008 R2 (ou posterior)

  **Requisitos recomendados**

  * CPU de 8 núcleos
  * 8 GB de memória
  * versão de 64 bits do Windows Server 2012 R2 (ou posterior)

  > [!NOTE]
  > O gateway não dá suporte ao Windows Server 2016 Core.

* **Considerações importantes**

  * Você pode instalar o gateway de dados local somente em um computador local, não em um controlador de domínio. No entanto, você não precisa instalar o gateway no mesmo computador que a fonte de dados. Além disso, você precisa de apenas um gateway para todas as suas fontes de dados, portanto, não é necessário instalar o gateway para cada fonte de dados.

    > [!TIP]
    > Para minimizar a latência, você pode instalar o gateway o mais próximo possível da fonte de dados ou no mesmo computador, supondo que você tenha permissões.

  * Instale o gateway em um computador conectado à Internet, sempre ligado e *não* vá para o estado de suspensão. Caso contrário, o gateway não poderá ser executado. Além disso, o desempenho pode ser afetado em uma rede sem fio.

  * Durante a instalação, você só pode entrar com uma [conta corporativa ou de estudante](../active-directory/sign-up-organization.md) gerenciada pelo Azure Active Directory (Azure AD), por exemplo @contoso.onmicrosoft.com,, e não uma conta B2B (convidado) do Azure ou uma @hotmail.com conta Microsoft pessoal, como ou @outlook.com. Certifique-se de usar a mesma conta de entrada ao registrar a instalação do gateway no portal do Azure criando um recurso de gateway. Você pode selecionar esse recurso de gateway ao criar a conexão de seu aplicativo lógico para sua fonte de dados local. [Por que devo usar uma conta corporativa ou de estudante do Azure AD?](#why-azure-work-school-account)

  > [!TIP]
  > Se você se inscreveu para uma oferta do Office 365 e não forneceu seu email de trabalho real, você pode ter um endereço de entrada parecido com este exemplo:`username@domain.onmicrosoft.com` 
  >
  > Para usar um conta Microsoft que tenha uma [assinatura padrão do Visual Studio](https://visualstudio.microsoft.com/vs/pricing/), primeiro [crie um diretório (locatário) no Azure Active Directory](../active-directory/develop/quickstart-create-new-tenant.md)ou use o diretório padrão, com seu conta Microsoft. 
  > Adicione um usuário com uma senha ao diretório e conceda a esse usuário acesso à sua assinatura. 
  > Em seguida, você pode entrar durante a instalação do gateway com esse nome de usuário e senha.

  * A região selecionada para a instalação do gateway determina o local em que você registra posteriormente o gateway no Azure criando um recurso do Azure. Ao criar esse recurso de gateway no Azure, você deve selecionar o *mesmo* local da instalação do gateway. A região padrão é o mesmo local que o locatário do Azure AD, que gerencia sua conta do Azure, mas você pode alterar o local durante a instalação do gateway.

  * Se você já tiver um gateway configurado com um instalador anterior à versão 14.16.6317.4, não será possível alterar o local do gateway executando o instalador mais recente. No entanto, você pode usar o instalador mais recente para configurar um novo gateway com o local desejado em vez disso.
  
    Se você tiver um instalador de gateway anterior à versão 14.16.6317.4, mas ainda não tiver instalado seu gateway, poderá baixar e usar o instalador mais recente.

## <a name="high-availability-support"></a>Suporte de alta disponibilidade

O gateway de dados local dá suporte à alta disponibilidade quando você tem mais de uma instalação de gateway e as configura como clusters. Se você tiver um gateway existente ao ir para criar outro gateway, você pode, opcionalmente, criar clusters de alta disponibilidade. Esses clusters organizam gateways em grupos que podem ajudar a evitar pontos únicos de falha. Além disso, todos os conectores de gateway de dados locais agora dão suporte à alta disponibilidade.

Para usar o gateway de dados local, examine estes requisitos e considerações:

* Você já deve ter pelo menos uma instalação de gateway na mesma assinatura do Azure que o gateway primário e a chave de recuperação para essa instalação.

* O gateway primário deve estar executando a atualização do gateway de novembro de 2017 ou posterior.

Depois de atender a esses requisitos, ao criar seu próximo gateway, selecione **Adicionar a um cluster de gateway existente**, selecione o gateway primário para o cluster e forneça a chave de recuperação para o gateway primário. Para obter mais informações, consulte [clusters de alta disponibilidade para o gateway de dados local](https://docs.microsoft.com/power-bi/service-gateway-high-availability-clusters).

<a name="install-gateway"></a>

## <a name="install-data-gateway"></a>Instalar o gateway de dados

1. [Baixe, salve e execute o instalador do gateway em um computador local](https://aka.ms/on-premises-data-gateway-installer).

1. Aceite o caminho de instalação padrão ou especifique o local no computador onde você deseja instalar o gateway.

1. Examine e aceite os termos de uso e a política de privacidade e, em seguida, escolha **instalar**.

   ![Aceitar os termos de uso e a política de privacidade](./media/logic-apps-gateway-install/accept-terms.png)

1. Depois que o gateway for instalado com êxito, forneça o endereço de email para sua conta corporativa ou de estudante e escolha **entrar**.

   ![Entrar com uma conta corporativa ou de estudante](./media/logic-apps-gateway-install/sign-in-gateway-install.png)

1. Escolha **registrar um novo gateway neste computador** > em**seguida**, que registra a instalação do gateway com o [serviço de nuvem do gateway](#gateway-cloud-service).

   ![Registar gateway](./media/logic-apps-gateway-install/register-new-gateway.png)

1. Forneça essas informações para a instalação do gateway:

   * O nome que você deseja para a instalação

   * A chave de recuperação que você deseja criar, que deve ter pelo menos oito caracteres

     > [!IMPORTANT]
     > Salve e mantenha sua chave de recuperação em um local seguro. Você precisa dessa chave ao alterar o local do gateway ou ao migrar, recuperar ou assumir um gateway existente.

   * Confirmação para sua chave de recuperação

     ![Configurar gateway](./media/logic-apps-gateway-install/set-up-gateway.png)

1. Verifique a região selecionada para o serviço de nuvem do gateway e o barramento de serviço do Azure que é usado pela instalação do seu gateway.

   ![Região de verificação](./media/logic-apps-gateway-install/check-region.png)

   > [!IMPORTANT]
   > Para alterar essa região depois de concluir a instalação do gateway, você precisará da chave de recuperação para essa instalação do gateway. Além disso, você deve desinstalar e reinstalar o gateway. Para obter mais informações, consulte [alterar local, migrar, recuperar ou assumir o gateway existente](#update-gateway-installation).

   *Por que alterar a região para a instalação do gateway?*

   Por exemplo, para reduzir a latência, você pode alterar a região do gateway para a mesma região que seu aplicativo lógico. Ou, você pode selecionar a região mais próxima de sua fonte de dados local.    O *recurso de gateway no Azure* e seu aplicativo lógico podem ter locais diferentes.

1. Para aceitar a região padrão, escolha **Configurar**. Ou, para alterar a região padrão, siga estas etapas:

   1. Ao lado da região atual, selecione **alterar região**.

      ![Alterar região](./media/logic-apps-gateway-install/change-region.png)

   1. Na página seguinte, abra a lista **selecionar região** , selecione a região desejada e escolha **concluído**.

      ![Selecionar outra região](./media/logic-apps-gateway-install/select-region-gateway-install.png)

1. Depois que a página de confirmação for exibida, escolha **fechar**.

   O instalador confirma que seu gateway agora está online e pronto para uso.

   ![Gateway concluído](./media/logic-apps-gateway-install/finished-gateway-default-location.png)

1. Agora, Registre seu gateway no Azure [criando um recurso do Azure para a instalação do gateway](../logic-apps/logic-apps-gateway-connection.md).

<a name="update-gateway-installation"></a>

## <a name="change-location-migrate-restore-or-take-over-existing-gateway"></a>Alterar local, migrar, restaurar ou assumir o gateway existente

Se for necessário alterar o local do gateway, mover a instalação do gateway para um novo computador, recuperar um gateway danificado ou apropriar-se de um gateway existente, você precisará da chave de recuperação que foi fornecida durante a instalação do gateway. Essa ação desconecta o gateway antigo.

1. No **painel de controle**do seu computador, vá para **programas e recursos**. Na lista de programas, selecione **Gateway de dados local**e escolha **desinstalar**.

1. [Reinstale o gateway de dados local](https://aka.ms/on-premises-data-gateway-installer).

1. Depois que o instalador for aberto, entre com a mesma conta corporativa ou de estudante usada anteriormente para instalar o gateway.

1. Selecione **migrar, restaurar ou tomada um gateway existente**e, em seguida, escolha **Avançar**.

   ![Selecione "migrar, restaurar ou tomada um gateway existente"](./media/logic-apps-gateway-install/migrate-recover-take-over-gateway.png)

1. Em **gateways disponíveis** ou **clusters de gateway disponíveis**, selecione a instalação do gateway que você deseja alterar. Insira a chave de recuperação para a instalação do gateway.

   ![Selecionar gateway primário](./media/logic-apps-gateway-install/select-existing-gateway.png)

1. Para alterar a região, selecione **alterar região** e nova região.

1. Quando terminar, escolha **Configurar**.

## <a name="configure-proxy-or-firewall"></a>Configurar proxy ou firewall

O gateway de dados local cria uma conexão de saída com o [barramento de serviço do Azure](https://azure.microsoft.com/services/service-bus/). Se o seu ambiente de trabalho exigir que o tráfego passe por um proxy para acessar a Internet, essa restrição poderá impedir que o gateway de dados se conecte ao serviço de nuvem do gateway. Para determinar se sua rede usa um proxy, leia este artigo em superuser.com:

[Como fazer saber qual servidor proxy estou usando? (SuperUser.com)](https://superuser.com/questions/346372/how-do-i-know-what-proxy-server-im-using)

Para fornecer informações de proxy para seu gateway, consulte [definir configurações de proxy](https://docs.microsoft.com/power-bi/service-gateway-proxy). Para verificar se o proxy ou o firewall pode bloquear conexões, confirme se o computador pode realmente se conectar à Internet e ao [barramento de serviço do Azure](https://azure.microsoft.com/services/service-bus/). Em um prompt do PowerShell, execute este comando:

`Test-NetConnection -ComputerName watchdog.servicebus.windows.net -Port 9350`

> [!NOTE]
> Esse comando só testa conectividade de rede e conectividade com o barramento de serviço do Azure. O comando não faz nada com o gateway ou o serviço de nuvem do gateway que criptografa e armazena suas credenciais e detalhes do gateway. 
>
> Além disso, esse comando só está disponível no Windows Server 2012 R2 ou posterior e Windows 8.1 ou posterior. Em versões anteriores do sistema operacional, você pode usar o Telnet para testar a conectividade. Saiba mais sobre as [soluções híbridas e do barramento de serviço do Azure](../service-bus-messaging/service-bus-messaging-overview.md).

Os resultados devem ser semelhantes a este exemplo com **TcpTestSucceeded** definido como **true**:

```text
ComputerName           : watchdog.servicebus.windows.net
RemoteAddress          : 70.37.104.240
RemotePort             : 5672
InterfaceAlias         : vEthernet (Broadcom NetXtreme Gigabit Ethernet - Virtual Switch)
SourceAddress          : 10.120.60.105
PingSucceeded          : False
PingReplyDetails (RTT) : 0 ms
TcpTestSucceeded       : True
```

Se **TcpTestSucceeded** não estiver definido como **true**, o gateway poderá ser bloqueado por um firewall. Se você quiser ser abrangente, substitua os valores **ComputerName** e **Port** pelos valores listados em [configurar portas](#configure-ports) neste artigo.

O firewall também pode bloquear as conexões que o barramento de serviço do Azure faz para os data centers do Azure. Se esse cenário ocorrer, aprove (desbloqueie) todos os endereços IP para esses datacenters em sua região. Para esses endereços IP, [obtenha a lista de endereços IP do Azure aqui](https://www.microsoft.com/download/details.aspx?id=41653).

## <a name="configure-ports"></a>Configurar portas

O gateway cria uma conexão de saída com o [barramento de serviço do Azure](https://azure.microsoft.com/services/service-bus/) e se comunica nas portas de saída: TCP 443 (padrão), 5671, 5672, 9350 até 9354. O gateway não requer portas de entrada. Saiba mais sobre as [soluções híbridas e do barramento de serviço do Azure](../service-bus-messaging/service-bus-messaging-overview.md).

O gateway usa esses nomes de domínio totalmente qualificados:

| Nomes de domínio | Portas de saída | Descrição |
| ------------ | -------------- | ----------- |
| *.analysis.windows.net | 443 | HTTPS |
| *.core.windows.net | 443 | HTTPS |
| *.frontend.clouddatahub.net | 443 | HTTPS |
| *.login.windows.net | 443 | HTTPS |
| *.microsoftonline-p.com | 443 | Usado para autenticação, dependendo da configuração. |
| *.msftncsi.com | 443 | Usado para testar a conectividade com a Internet quando o gateway está inacessível pelo serviço do Power BI. |
| *.servicebus.windows.net | 443, 9350-9354 | Ouvintes na retransmissão do barramento de serviço sobre TCP (requer 443 para aquisição de token de controle de acesso) |
| *.servicebus.windows.net | 5671-5672 | Advanced Message Queuing Protocol (AMQP) |
| login.microsoftonline.com | 443 | HTTPS |
||||

Em alguns casos, as conexões do barramento de serviço do Azure são feitas com endereços IP em vez de nomes de domínio totalmente qualificados. Portanto, talvez você queira desbloquear os endereços IP para sua região de dados em seu firewall. Para permitir o acesso a endereços IP em vez de domínios, você pode baixar e usar a [lista de intervalos de IP Microsoft Azure datacenter](https://www.microsoft.com/download/details.aspx?id=41653). Os endereços IP nessa lista estão em notação [CIDR (roteamento entre domínios sem classificação)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) .

### <a name="force-https-communication-with-azure-service-bus"></a>Forçar comunicação HTTPS com o barramento de serviço do Azure

Alguns proxies permitem o tráfego somente para as portas 80 e 443. Por padrão, a comunicação com o barramento de serviço do Azure ocorre em portas diferentes de 443. Você pode forçar o gateway a se comunicar com o barramento de serviço do Azure por HTTPS em vez de TCP direto, mas isso pode reduzir significativamente o desempenho. Para executar essa tarefa, siga estas etapas:

1. Navegue até o local do cliente de gateway de dados local, que geralmente pode ser encontrado aqui:```C:\Program Files\On-premises data gateway\Microsoft.PowerBI.EnterpriseGateway.exe```

   Caso contrário, para localizar o local do cliente, abra o console de serviços no mesmo computador, localize o **serviço do gateway de dados local**e exiba o **caminho para a propriedade executável** .

1. Abra este arquivo de *configuração* : **Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config**

1. Altere o valor de **ServiceBusSystemConnectivityModeString** de **AutoDetect** para **https**:

   ```html
   <setting name="ServiceBusSystemConnectivityModeString" serializeAs="String">
      <value>Https</value>
   </setting>
   ```

<a name="windows-service-account"></a>

## <a name="windows-service-account"></a>Conta de serviço do Windows

No computador em que você instala o gateway de dados local, o gateway é executado como uma conta de serviço do Windows denominada "serviço de gateway de dados local". No entanto, o gateway usa o nome "NT SERVICE\PBIEgwService" para suas credenciais de conta "fazer logon como". Por padrão, o gateway tem as permissões "fazer logon como um serviço" no computador em que você instala o gateway. A conta de serviço do Windows para o gateway geralmente é diferente da conta que você usa para se conectar a fontes de dados locais e da conta corporativa ou de estudante que você usa para entrar nos serviços de nuvem.

Para criar e manter o gateway no portal do Azure, essa conta de serviço do Windows deve ter pelo menos permissões de **colaborador** . Para verificar essas permissões, consulte [gerenciar o acesso usando o RBAC e o portal do Azure](../role-based-access-control/role-assignments-portal.md).

<a name="restart-gateway"></a>

## <a name="restart-gateway"></a>Reiniciar gateway

O gateway de dados é executado como um serviço de janela, assim como qualquer outro serviço do Windows, você pode iniciar e parar o gateway de várias maneiras. Por exemplo, você pode abrir um prompt de comando com permissões elevadas no computador em que o gateway está em execução e executar um dos comandos:

* Para interromper o serviço, execute este comando:
  
  `net stop PBIEgwService`

* Para iniciar o serviço, execute este comando:
  
  `net start PBIEgwService`

## <a name="tenant-level-administration"></a>Administração no nível do locatário

Atualmente, não há um único local em que os administradores de locatários podem gerenciar todos os gateways que outros usuários instalaram e configuraram. Se você for um administrador de locatários, talvez queira que os usuários em sua organização adicionem você como um administrador para cada gateway que eles instalarem. Dessa forma, você pode gerenciar todos os gateways em sua organização por meio da página Configurações do gateway ou dos [comandos do PowerShell](/data-integration/gateway/service-gateway-powershell-support).

<a name="gateway-cloud-service"></a>

## <a name="how-does-the-gateway-work"></a>Como funciona o gateway?

O gateway de dados facilita a comunicação rápida e segura entre seu aplicativo lógico, o serviço de nuvem do gateway e sua fonte de dados local. O serviço de nuvem do gateway criptografa e armazena suas credenciais de fonte de dados e detalhes do gateway. O serviço também roteia consultas e seus resultados entre seu aplicativo lógico, o gateway de dados local e sua fonte de dados local.

O gateway funciona com firewalls e usa apenas conexões de saída. Todo o tráfego é originado como tráfego de saída seguro do agente de gateway. O gateway retransmite dados de fontes locais em canais criptografados por meio do barramento de serviço do Azure. Esse barramento de serviço cria um canal entre o gateway e o serviço de chamada, mas não armazena nenhum dado. Todos os dados que trafegam pelo gateway são criptografados.

![diagram-for-on-premises-data-gateway-flow](./media/logic-apps-gateway-install/how-on-premises-data-gateway-works-flow-diagram.png)

Estas etapas descrevem o que acontece quando um usuário na nuvem interage com um elemento que está conectado à sua fonte de dados local:

1. O serviço de nuvem do gateway cria uma consulta, juntamente com as credenciais criptografadas para a fonte de dados, e envia a consulta para a fila para que o gateway seja processado.

1. O serviço de nuvem do gateway analisa a consulta e envia a solicitação para o barramento de serviço do Azure.

1. O gateway de dados local sonda o barramento de serviço do Azure para solicitações pendentes.

1. O gateway Obtém a consulta, descriptografa as credenciais e conecta-se à fonte de dados com essas credenciais.

1. O gateway envia a consulta à fonte de dados para execução.

1. Os resultados são enviados da fonte de dados de volta para o gateway e, em seguida, para o serviço de nuvem do gateway. Em seguida, o serviço de nuvem do gateway usa os resultados.

<a name="faq"></a>

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="general"></a>Geral

**P**: Preciso de um gateway para as fontes de dados na nuvem, como o Azure SQL Database? <br/>
**A**: Não, o gateway se conecta somente a fontes de dados locais.

**P**: O gateway precisa ser instalado no mesmo computador que a fonte de dados? <br/>
**A**: Não, o gateway se conecta à fonte de dados usando as informações de conexão fornecidas. Considere o gateway como um aplicativo cliente nesse sentido. O gateway só precisa da capacidade de se conectar ao nome do servidor que foi fornecido.

<a name="why-azure-work-school-account"></a>

**P**: Por que é necessário usar uma conta corporativa ou de estudante para entrar? <br/>
**A**: Você só pode usar uma conta corporativa ou de estudante ao instalar o gateway de dados local. Sua conta de entrada é armazenada em um locatário gerenciado pelo Azure Active Directory (Azure AD). Normalmente, o UPN (nome principal de usuário) da sua conta do Azure AD corresponde ao endereço de email.

**P**: Onde as minhas credenciais são armazenadas? <br/>
**A**: As credenciais inseridas para uma fonte de dados são criptografadas e armazenadas no serviço de nuvem do gateway. As credenciais são descriptografadas no gateway de dados local.

**P**: Há algum requisito de largura de banda de rede? <br/>
**A**: Verifique se sua conexão de rede tem boa taxa de transferência. Cada ambiente é diferente e a quantidade de dados enviados pode afetar os resultados. Para garantir um nível de taxa de transferência entre sua fonte de dados local e os data centers do Azure, experimente o [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/). Para ajudar a medir sua taxa de transferência, experimente uma ferramenta externa, como o teste de velocidade do Azure.

**P**: Qual é a latência para executar consultas em uma fonte de dados do gateway? Qual é a melhor arquitetura? <br/>
**A**: Para reduzir a latência de rede, instale o gateway o mais próximo possível da fonte de dados. Se você puder instalar o gateway na fonte de dados real, essa proximidade minimizará a latência introduzida. Além disso, considere a proximidade com os data centers do Azure. Por exemplo, se o serviço usar o datacenter oeste dos EUA e você tiver SQL Server hospedado em uma VM do Azure, talvez você queira também a VM do Azure na região oeste dos EUA. Essa proximidade minimiza a latência e evita encargos de saída na VM do Azure.

**P**: Como os resultados são enviados de volta para a nuvem? <br/>
**A**: Os resultados são enviados por meio do barramento de serviço do Azure.

**P**: Há conexões de entrada para o gateway a partir da nuvem? <br/>
**A**: Não, o gateway usa conexões de saída para o barramento de serviço do Azure.

**P**: E se eu bloquear conexões de saída? O que é necessário abrir? <br/>
**A**: Consulte as portas e os hosts que o gateway usa.

**P**: O que é o serviço do Windows real chamado? <br/>
**A**: Na guia serviços do Gerenciador de tarefas, o nome do serviço é "PBIEgwService" ou Power BI serviço de gateway corporativo. No console de serviços, o nome do serviço é "serviço de gateway de dados local". O serviço do Windows usa "NT SERVICE\PBIEgwService" como o SID do serviço (SSID).

**P**: O serviço Windows de gateway pode ser executado com uma conta de Azure Active Directory? <br/>
**A**: Não, o serviço do Windows deve ter uma conta do Windows válida.

### <a name="disaster-recovery"></a>Recuperação após desastre

**P**: Quais opções estão disponíveis para a recuperação de desastre? <br/>
**A**: Você pode usar a chave de recuperação para restaurar ou mover um gateway. Ao instalar o gateway, especifique a chave de recuperação.

**P**: Qual é o benefício da chave de recuperação? <br/>
**A**: A chave de recuperação fornece uma maneira de migrar ou recuperar as configurações do gateway após um desastre.

## <a name="troubleshooting"></a>Resolução de problemas

Esta seção aborda alguns problemas comuns que você pode ter durante a configuração e o uso do gateway de dados local.

**P**: Por que a instalação do meu gateway falhou? <br/>
**A**: Esse problema pode ocorrer se o software antivírus no computador de destino estiver desatualizado. Você pode atualizar o software antivírus ou desabilitar o software antivírus, mas apenas durante a instalação do gateway e, em seguida, habilitar o software novamente.

**P**: Por que não vejo minha instalação de gateway Quando crio o recurso de gateway no Azure? <br/>
**A**: Esse problema pode ocorrer por esses motivos:

* A instalação do gateway já está registrada e reivindicada por outro recurso de gateway no Azure. As instalações de gateway não aparecem na lista de instâncias depois que os recursos de gateway são criados para eles. Para verificar os registros de gateway no portal do Azure, examine todos os recursos do Azure com o tipo de **gateways de dados local** para *todas as* assinaturas do Azure.

* A identidade do Azure AD para a pessoa que instalou o gateway é diferente da pessoa que se conectou ao portal do Azure. Verifique se você está conectado com a mesma identidade que instalou o gateway.

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

**P**: Onde estão os logs do gateway? <br/>
**A**: Consulte a [seção **logs** ](#logs) mais adiante neste artigo.

**P**: Como posso ver quais consultas estão sendo enviadas para a fonte de dados local? <br/>
**A**: Você pode habilitar o rastreamento de consulta, que inclui as consultas que são enviadas. Lembre-se de alterar o rastreamento de consulta de volta para o valor original ao concluir a solução de problemas. Deixar o rastreamento de consulta ativado cria logs maiores.

Você também pode examinar as ferramentas que sua fonte de dados tem para rastrear consultas. Por exemplo, você pode usar eventos estendidos ou o SQL Profiler para SQL Server e Analysis Services.

### <a name="outdated-gateway-version"></a>Versão do gateway desatualizada

Muitos problemas podem surgir quando a versão do gateway se tornar desatualizada. Como uma boa prática geral, verifique se você tem a versão mais recente. Se você não tiver atualizado o gateway por um mês ou mais, poderá considerar a instalação da versão mais recente do gateway e ver se você pode reproduzir o problema.

### <a name="error-failed-to-add-user-to-group--2147463168-pbiegwservice-performance-log-users"></a>Erro: Falha ao adicionar usuário ao grupo. (-2147463168 PBIEgwService usuários de log de desempenho)

Você poderá receber esse erro se tentar instalar o gateway em um controlador de domínio, que não tem suporte. Certifique-se de implantar o gateway em um computador que não seja um controlador de domínio.

<a name="logs"></a>

### <a name="logs"></a>Registos

Para ajudá-lo a solucionar problemas, sempre comece coletando e revisando os logs do gateway. Você tem várias maneiras de coletar os logs, mas a opção mais simples depois de instalar o gateway é por meio da interface do usuário do instalador do gateway.

1. No computador, abra o instalador do gateway de dados local.

1. No menu à esquerda, selecione **diagnóstico**.

1. Em **logs de gateway**, selecione **Exportar logs**.

   ![Exportar logs do instalador do gateway](./media/logic-apps-gateway-install/export-logs.png)

Aqui estão outros locais onde você pode encontrar vários logs:

| Tipo de registo | Location |
|----------|----------|
| **Logs do instalador** | %localappdata%\Temp\On-premises_data_gateway_ <*aaaammdd*>. <*número*>. log |
| **Logs de configuração** | C:\Users\<*username*> \AppData\Local\Microsoft\On-premises data gateway\GatewayConfigurator <*aaaammdd*>. <*número*>. log |
| **Logs de serviço do gateway corporativo** | C:\Users\PBIEgwService\AppData\Local\Microsoft\On-premises data gateway\Gateway <*aaaammdd*>. <*número*>. log |
|||

**Logs de eventos**

Para localizar os logs de eventos para o gateway, siga estas etapas:

1. No computador com a instalação do gateway, abra o **Visualizador de eventos**.

1. Expanda**os logs de aplicativos e serviços** **Visualizador de eventos (local)**  > .

1. Selecione o **serviço do gateway de dados local**.

   ![Exibir logs de eventos do gateway](./media/logic-apps-gateway-install/event-viewer.png)

### <a name="review-slow-query-performance"></a>Examinar o desempenho de consulta lento

Se você encontrar consultas executadas lentamente por meio do gateway, poderá ativar o log adicional que gera consultas e suas durações. Esses logs podem ajudá-lo a descobrir quais consultas estão lentas ou de execução longa. Para ajustar o desempenho da consulta, talvez seja necessário modificar a fonte de dados, por exemplo, ajustar índices para consultas de SQL Server.

Para determinar a duração de uma consulta, siga estas etapas:

1. Navegue até o mesmo local que o cliente de gateway, que geralmente pode ser encontrado aqui:```C:\Program Files\On-premises data gateway```

   Caso contrário, para localizar o local do cliente, abra o console de serviços no mesmo computador, localize o **serviço do gateway de dados local**e exiba o **caminho para a propriedade executável** .

1. Abra e edite esses arquivos de configuração conforme descrito:

   * **Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config**

     Nesse arquivo, altere o valor de **EmitQueryTraces** de **false** para **true** para que o gateway possa registrar consultas enviadas do gateway para uma fonte de dados:

     ```html
     <setting name="EmitQueryTraces" serializeAs="String">
        <value>true</value>
     </setting>
     ```

     > [!IMPORTANT]
     > A ativação da configuração EmitQueryTraces pode aumentar significativamente o tamanho do log com base no uso do gateway. Depois de concluir a revisão dos logs, verifique se você redefiniu EmitQueryTraces para **false** novamente, em vez de deixar essa configuração para a longo prazo.

   * **Microsoft.PowerBI.DataMovement.Pipeline.Diagnostics.dll.config**

     Para ter as entradas detalhadas do log do gateway, incluindo entradas que mostram a duração, altere o valor de **TracingVerbosity** de **4** para **5** executando uma das etapas:

     * Nesse arquivo de configuração, altere o valor de **TracingVerbosity** de **4** para **5**

       ```html
       <setting name="TracingVerbosity" serializeAs="String">
          <value>5</value>
       </setting>
       ```

     * Abra o instalador do gateway, selecione **diagnóstico**, ative o **registro em log adicional**e, em seguida, escolha **aplicar**:

       ![Ativar log adicional](./media/logic-apps-gateway-install/turn-on-additional-logging.png)

     > [!IMPORTANT]
     > A ativação da configuração TracingVerbosity pode aumentar significativamente o tamanho do log com base no uso do gateway. Depois de concluir a revisão dos logs, desative o **registro em log adicional** no instalador do gateway ou redefina TracingVerbosity para **4** novamente no arquivo de configuração, em vez de deixar essa configuração em um longo prazo.

1. Para localizar a duração de uma consulta, siga estas etapas:

   1. [Exporte](#logs) e abra o log do gateway.

   1. Para localizar uma consulta, procure um tipo de atividade, por exemplo:

      | Tipo de atividade | Descrição |
      |---------------|-------------|
      | MGEQ | Consultas que são executadas em ADO.NET |
      | MGEO | Consultas que são executadas sobre OLEDB |
      | MGEM | Consultas executadas a partir do mecanismo de mashup |
      |||

   1. Observe o segundo GUID, que é a ID da solicitação.

   1. Continue pesquisando o tipo de atividade até encontrar uma entrada denominada "FireActivityCompletedSuccessfullyEvent" que tenha uma duração em milissegundos. Confirme se a entrada tem a mesma ID de solicitação, por exemplo:

      ```text
      DM.EnterpriseGateway Verbose: 0 : 2016-09-26T23:08:56.7940067Z DM.EnterpriseGateway    baf40f21-2eb4-4af1-9c59-0950ef11ec4a    5f99f566-106d-c8ac-c864-c0808c41a606    MGEQ    21f96cc4-7496-bfdd-748c-b4915cb4b70c    B8DFCF12 [DM.Pipeline.Common.TracingTelemetryService] Event: FireActivityCompletedSuccessfullyEvent (duration=5004)
      ```

      > [!NOTE]
      > A entrada "FireActivityCompletedSuccessfullyEvent" é uma entrada detalhada e não é registrada, a menos que a configuração "TracingVerbosity" esteja no nível 5.

### <a name="trace-traffic-with-fiddler"></a>Rastrear o tráfego com o Fiddler

O [Fiddler](https://www.telerik.com/fiddler) é uma ferramenta gratuita da Telerik que monitora o tráfego http. Você pode examinar esse tráfego com o serviço do Power BI do computador cliente. Esse serviço pode mostrar erros e outras informações relacionadas.

## <a name="next-steps"></a>Passos Seguintes

* [Conectar-se a dados locais de aplicativos lógicos](../logic-apps/logic-apps-gateway-connection.md)
* [Recursos de integração corporativa](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Conectores para Azure Logic Apps](../connectors/apis-list.md)
