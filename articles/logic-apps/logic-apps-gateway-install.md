---
title: Instalar o gateway de dados no local - Azure Logic Apps | Documentos da Microsoft
description: Antes de poder aceder a dados no local do Azure Logic Apps, transfira e instale o gateway de dados no local
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: arthii, LADocs
ms.topic: article
ms.date: 10/01/2018
ms.openlocfilehash: 10a6e5c33f6a3c23d98e6eb3380de0d6dc6ac216
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544460"
---
# <a name="install-on-premises-data-gateway-for-azure-logic-apps"></a>Instalar o gateway de dados no local para o Azure Logic Apps

Antes de poder ligar a origens de dados no local do Azure Logic Apps, transfira e instale o gateway de dados no local num computador local. O gateway funciona como uma ponte que fornece a transferência de dados rápida e encriptação entre origens de dados no local (não na cloud) e as aplicações lógicas. Este artigo mostra como pode transferir, instalar e configurar o gateway de dados no local. 

Pode utilizar a mesma instalação do gateway com outros serviços, como o Power BI, Microsoft Flow, PowerApps e do Azure Analysis Services. Saiba mais sobre [como funciona o gateway de dados](#gateway-cloud-service).

<a name="supported-connections"></a>

O gateway suporta [conectores no local](../connectors/apis-list.md#on-premises-connectors) no Azure Logic Apps para estas origens de dados:

*   BizTalk Server 2016
*   Sistema de Ficheiros
*   IBM DB2  
*   IBM Informix
*   IBM MQ
*   MySQL
*   Oracle Database
*   PostgreSQL
*   SAP Application Server 
*   SAP Message Server
*   SharePoint Server
*   SQL Server
*   Teradata

Para obter informações sobre como utilizar o gateway com outros serviços, veja estes artigos:

* [Gateway de dados do Microsoft Power BI no local](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem/)
* [Gateway de dados do Microsoft PowerApps no local](https://powerapps.microsoft.com/tutorials/gateway-management/)
* [Gateway de dados do Microsoft Flow no local](https://flow.microsoft.com/documentation/gateway-manage/)
* [Gateway de dados no local do Analysis Services do Azure](../analysis-services/analysis-services-gateway.md)

<a name="requirements"></a>

## <a name="prerequisites"></a>Pré-requisitos

* R [conta escolar ou profissional](../active-directory/fundamentals/sign-up-organization.md) que tem um [subscrição do Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/governance/resource-consistency/azure-resource-access) 

  Durante a instalação do gateway, entrar para esta conta para que pode associar a instalação do gateway com a sua subscrição do Azure. 
  Mais tarde, também usar conta mesmo quando cria um recurso do Azure para a sua instalação do gateway no portal do Azure. 
  Se não tiver uma subscrição do Azure, <a href="https://azure.microsoft.com/free/" target="_blank">Inscreva-se uma conta gratuita do Azure</a>.

* Eis os requisitos para o seu computador local:

  **Requisitos mínimos**

  * .NET Framework 4.5.2
  * versão de 64 bits do Windows 7 ou Windows Server 2008 R2 (ou posterior)

  **Requisitos recomendados**

  * CPU de 8 núcleos
  * 8 GB de memória
  * versão de 64 bits do Windows Server 2012 R2 (ou posterior)

* **Considerações importantes**

  * Pode instalar o gateway de dados no local apenas num computador local, não é um controlador de domínio. No entanto, não precisa de instalar o gateway no mesmo computador como origem de dados. Além disso, precisa apenas um gateway para todas as suas origens de dados, portanto não precisa de instalar o gateway para cada origem de dados.

    > [!TIP]
    > Para minimizar a latência, pode instalar o gateway mais próximo possível para a sua origem de dados ou no mesmo computador, supondo que tenha as permissões.

  * Instalar o gateway num computador que está ligado à internet, sempre ativado, e *não* go no modo de suspensão. Caso contrário, o gateway não pode ser executado. 
  Além disso, o desempenho poderá ser afetado por uma rede sem fio.

  * Durante a instalação, pode apenas iniciar sessão com uma [conta escolar ou profissional](../active-directory/sign-up-organization.md) que é gerido pelo Azure Active Directory (Azure AD), por exemplo, @contoso.onmicrosoft.come não um B2B do Azure conta (convidado) ou um pessoal da Microsoft conta, por exemplo, @hotmail.com ou @outlook.com. 
  Certifique-se de que utilizar a mesma conta de início de sessão ao registar a instalação do gateway no portal do Azure através da criação de um recurso de gateway. 
  Em seguida, pode selecionar este recurso de gateway ao criar a ligação da sua aplicação lógica à sua origem de dados no local. 
  [Por que motivo deve utilizar um trabalho do Azure AD ou conta escolar?](#why-azure-work-school-account)

  > [!TIP]
  > Se se inscreveu numa oferta do Office 365 e não forneceu o seu e-mail profissional real, pode ter um endereço de início de sessão que este aspeto: `username@domain.onmicrosoft.com` 
  >
  > Para utilizar uma conta Microsoft que tem um [subscrição do Visual Studio Standard](https://visualstudio.microsoft.com/vs/pricing/), primeiro [criar um diretório (inquilino) no Azure Active Directory](../active-directory/develop/quickstart-create-new-tenant.md), ou utilizar o diretório predefinido, com a sua conta Microsoft. 
  > Adicionar um utilizador com uma palavra-passe para o diretório e, em seguida, conceder acesso a esse utilizador à sua subscrição. 
  > Em seguida, pode iniciar sessão durante a instalação do gateway com este nome de utilizador e palavra-passe.

  * A região que selecionou para a sua instalação do gateway determina a localização em que mais tarde registar o gateway no Azure através da criação de um recurso do Azure. 
  Quando cria este recurso de gateway no Azure, tem de selecionar o *mesmo* localização como a instalação do gateway. A região predefinida é a mesma localização que o inquilino do Azure AD, que gere a sua conta do Azure, mas pode alterar a localização durante a instalação do gateway.

  * Se já tiver um gateway que configurou com um instalador anteriores à versão 14.16.6317.4, não é possível alterar a localização do seu gateway ao executar o instalador mais recente. No entanto, pode utilizar o instalador mais recente para configurar um novo gateway com a localização do que mesmo em vez disso.
  
    Se tiver um programa de instalação do gateway que é anterior à versão 14.16.6317.4, mas ainda não instalou o gateway, pode transferir e utilizar o instalador mais recente em vez disso.

## <a name="high-availability-support"></a>Suporte de elevada disponibilidade

O gateway de dados no local suporta elevada disponibilidade, quando tiver mais do que uma instalação do gateway e configurá-los como clusters. Se tiver um gateway existente quando vai para criar outro gateway, opcionalmente, pode criar clusters de elevada disponibilidade. Estes clusters organizam gateways em grupos que podem ajudar a evitar pontos únicos de falha. Além disso, todos os conectores de gateway de dados no local agora suportam elevada disponibilidade.

Para utilizar o gateway de dados no local, reveja estes requisitos e considerações:

* Já tem de ter instalação de pelo menos um gateway na mesma subscrição do Azure como o gateway principal e a chave de recuperação para essa instalação. 

* O gateway primário tem de executar a atualização de gateway de Novembro de 2017 ou posterior.

Após o cumprimento destes requisitos, ao criar o gateway seguinte, selecione **adicionar a um cluster de gateway existente**, selecione o gateway principal para o seu cluster e fornecer a chave de recuperação para esse gateway primário.
Para obter mais informações, consulte [clusters de elevada disponibilidade para o gateway de dados no local](https://docs.microsoft.com/power-bi/service-gateway-high-availability-clusters).

<a name="install-gateway"></a>

## <a name="install-data-gateway"></a>Instalar o gateway de dados

1. [Transferir, guardar e executar o instalador do gateway num computador local](https://aka.ms/on-premises-data-gateway-installer).

2. Aceite o caminho de instalação predefinido ou especifique a localização no computador onde pretende instalar o gateway.

3. Reveja e aceite os termos de utilização e declaração de privacidade e, em seguida, escolha **instalar**.

   ![Aceitar os termos de utilização e declaração de privacidade](./media/logic-apps-gateway-install/accept-terms.png)

4. Depois do gateway é instalado com êxito, forneça o endereço de e-mail para a sua conta escolar ou profissional e escolha **iniciar sessão**.

   ![Inicie sessão com a conta escolar ou profissional](./media/logic-apps-gateway-install/sign-in-gateway-install.png)

5. Escolher **registar um novo gateway neste computador** > **próxima**, que regista a instalação do gateway com o [serviço cloud do gateway](#gateway-cloud-service). 

   ![Registar gateway](./media/logic-apps-gateway-install/register-new-gateway.png)

6. Forneça estas informações para a sua instalação do gateway:

   * O nome que pretende para a sua instalação 

   * A chave de recuperação que pretende criar, que tem de ter, pelo menos, oito carateres

     > [!IMPORTANT]
     > Guarde e manter a sua chave de recuperação num local seguro. Precisará desta chave quando alterar a localização do gateway, ou quando migrar, recuperar ou assumir um gateway existente.

   * Confirmação para a sua chave de recuperação 

     ![Configurar o gateway](./media/logic-apps-gateway-install/set-up-gateway.png)

7. Verifique a região selecionada para o serviço cloud do gateway e o Azure Service Bus, que é utilizado pela sua instalação do gateway. 

   ![Região de verificação](./media/logic-apps-gateway-install/check-region.png)

   > [!IMPORTANT]
   > Para alterar esta região depois de concluir a instalação do gateway, terá da chave de recuperação para essa instalação do gateway. Além disso, tem de desinstalar e reinstalar o gateway. Para obter mais informações, consulte [alterar localização, migrar, recuperar ou assumir gateway existente](#update-gateway-installation).

   *Por que alterar a região para a sua instalação do gateway?* 

   Por exemplo, para reduzir a latência, poderá alterar região do seu gateway à mesma região que a sua aplicação lógica. 
   Em alternativa, poderá selecionar a região mais próxima da sua origem de dados no local. 
   Sua *recurso de gateway no Azure* e a aplicação lógica pode ter diferentes localizações.

8. Para aceitar a região predefinida, escolha **configurar**. Em alternativa, para alterar a região predefinida, siga estes passos:

   1. Junto a região atual, selecione **alterar região**. 

      ![Alterar região](./media/logic-apps-gateway-install/change-region.png)

   2. Na página seguinte, abra a **selecionar região** , selecione a região que pretende e escolha **feito**.

      ![Selecione outra região](./media/logic-apps-gateway-install/select-region-gateway-install.png)

9. Depois de aparece a página de confirmação, escolha **fechar**. 

   O instalador confirma que o seu gateway agora está online e pronto a utilizar.

   ![Gateway concluído](./media/logic-apps-gateway-install/finished-gateway-default-location.png)

10. Agora registar o gateway no Azure mediante [criar um recurso do Azure para a sua instalação do gateway](../logic-apps/logic-apps-gateway-connection.md). 

<a name="update-gateway-installation"></a>

## <a name="change-location-migrate-restore-or-take-over-existing-gateway"></a>Alterar a localização, migrar, restaurar ou assumir gateway existente

Se deve alterar a localização do seu gateway, mover a instalação de gateway para um novo computador, recuperar um gateway danificado ou assumir a propriedade para um gateway existente, terá da chave de recuperação que foi fornecida durante a instalação do gateway. Esta ação desliga o gateway antigo.

1. Do seu computador **painel de controlo**, aceda à **programas e funcionalidades**. Na lista de programas, selecione **gateway de dados no local**e, em seguida, escolha **desinstalação**.

2. [Reinstale o gateway de dados no local](https://aka.ms/on-premises-data-gateway-installer).

3. Depois de abre o instalador, inicie sessão com a mesma conta escolar ou que foi usada anteriormente para instalar o gateway.

4. Selecione **migrar, restaurar ou assumir um gateway existente**e, em seguida, escolha **próxima**.

   ![Selecione "Para migrar, restaurar ou assumir um gateway existente"](./media/logic-apps-gateway-install/migrate-recover-take-over-gateway.png)

5. Sob **gateways disponíveis** ou **clusters de gateway disponíveis**, selecione a instalação do gateway que pretende alterar. Introduza a chave de recuperação para a instalação do gateway. 

   ![Selecione o gateway principal](./media/logic-apps-gateway-install/select-existing-gateway.png)

6. Para alterar a região, selecione **alterar região** e a nova região.

7. Quando tiver terminado, escolha **configurar**.

## <a name="configure-proxy-or-firewall"></a>Configurar o proxy ou firewall

O gateway de dados no local cria uma ligação de saída para [do Azure Service Bus](https://azure.microsoft.com/services/service-bus/). Se o seu ambiente de trabalho requer que o tráfego passa através de um proxy para aceder à internet, esta restrição pode impedir o gateway de dados de estabelecer ligação ao serviço cloud do gateway. Para determinar se a sua rede utiliza um proxy, reveja este artigo em superuser.com: 

[Como posso saber que servidor de proxy estou usando? (SuperUser.com)](https://superuser.com/questions/346372/how-do-i-know-what-proxy-server-im-using) 

Para fornecer informações de proxy para o gateway, veja [configurar definições de proxy](https://docs.microsoft.com/power-bi/service-gateway-proxy). Para verificar se o proxy ou firewall poderá bloquear ligações, confirme se o seu computador, na verdade, pode ligar à internet e o [do Azure Service Bus](https://azure.microsoft.com/services/service-bus/). A partir de uma linha de comandos do PowerShell, execute este comando:

`Test-NetConnection -ComputerName watchdog.servicebus.windows.net -Port 9350`

> [!NOTE]
> Este comando testa apenas a conectividade de rede e conectividade para o Azure Service Bus. O comando não faz nada com o gateway ou o serviço de nuvem de gateway que encripta e armazena as suas credenciais e detalhes do gateway. 
>
> Além disso, este comando só está disponível no Windows Server 2012 R2 ou posterior e o Windows 8.1 ou posterior. Em versões anteriores do sistema operacional, pode utilizar o Telnet para testar a conectividade. Saiba mais sobre [soluções do Azure Service Bus e híbridas](../service-bus-messaging/service-bus-messaging-overview.md).

Os resultados devem ter um aspeto semelhantes a este exemplo com **TcpTestSucceeded** definida como **verdadeiro**:

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

Se **TcpTestSucceeded** não estiver definido como **verdadeiro**, o gateway poderá ser bloqueado por uma firewall. Se quiser ser abrangente, substitua a **nomedocomputador** e **porta** valores com os valores listados na [configurar portas](#configure-ports) neste artigo.

A firewall poderá bloquear ligações a que o Azure Service Bus facilita para os datacenters do Azure. Se este cenário ocorrer, aprovar (desbloquear) todos os endereços IP para os data Centers em sua região. Para esses endereços IP [obter a lista de endereços IP do Azure aqui](https://www.microsoft.com/download/details.aspx?id=41653).

## <a name="configure-ports"></a>Configurar portas

O gateway cria uma ligação de saída para [do Azure Service Bus](https://azure.microsoft.com/services/service-bus/) e comunica com de portas de saída: TCP 443 (predefinição), 5671, 5672, 9350 através da 9354. O gateway não precisa de portas de entrada. Saiba mais sobre [soluções do Azure Service Bus e híbridas](../service-bus-messaging/service-bus-messaging-overview.md).

O gateway utiliza esses nomes de domínio completamente qualificado:

| Nomes de domínio | Portas de saída | Descrição | 
| ------------ | -------------- | ----------- | 
| *.analysis.windows.net | 443 | HTTPS | 
| *.core.windows.net | 443 | HTTPS | 
| *.frontend.clouddatahub.net | 443 | HTTPS | 
| *.login.windows.net | 443 | HTTPS | 
| *.microsoftonline-p.com | 443 | Utilizado para autenticação, dependendo da configuração. | 
| *.msftncsi.com | 443 | Utilizado para testar a conectividade à internet, quando o gateway não está acessível pelo serviço do Power BI. | 
| *.servicebus.windows.net | 443, 9350-9354 | Ouvintes no reencaminhamento do Service Bus por TCP (precisa de 443 para aquisição de token de controlo de acesso) | 
| *.servicebus.windows.net | 5671-5672 | Avançadas Message Queuing Protocol (AMQP) | 
| login.microsoftonline.com | 443 | HTTPS | 
||||

Em alguns casos, as conexões de barramento de serviço do Azure são feitas com endereços IP em vez de nomes de domínio completamente qualificado. Dessa forma, pode querer permitir endereços IP para a sua região de dados na sua firewall. Para permitir endereços IP em vez de domínios, pode transferir e utilizar o [lista de intervalos de IP de Datacenter do Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Os endereços IP nesta lista estão na [Classless entre domínios encaminhamento (CIDR)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) notação.

### <a name="force-https-communication-with-azure-service-bus"></a>Forçar a comunicação HTTPS com o Azure Service Bus

Alguns proxies permitem tráfego através de apenas para as portas 80 e 443. Por predefinição, a comunicação com o Azure Service Bus ocorre nas portas sem ser a 443.
Pode forçar o gateway a comunicar com o Azure Service Bus através de HTTPS, em vez de TCP direto, mas ao fazê-lo por isso, pode reduzir significativamente o desempenho. Para executar essa tarefa, siga estes passos:

1. Navegue até à localização para o cliente de gateway de dados no local, que normalmente pode ser encontrado aqui: ```C:\Program Files\On-premises data gateway\Microsoft.PowerBI.EnterpriseGateway.exe```

   Caso contrário, para encontrar a localização do cliente, abra a consola de serviços no mesmo computador, encontre **serviço de gateway de dados no local**e ver o **caminho para o executável** propriedade.

2. Abrir isto *configuração* ficheiro: **Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config**

3. Alteração da **ServiceBusSystemConnectivityModeString** partir da **AutoDetect** para **Https**:

   ```html
   <setting name="ServiceBusSystemConnectivityModeString" serializeAs="String">
      <value>Https</value>
   </setting>
   ```

<a name="windows-service-account"></a>

## <a name="windows-service-account"></a>Conta de serviço do Windows

No computador onde instalou o gateway de dados no local, o gateway é executado como uma conta de serviço do Windows com o nome "No local o serviço de gateway de dados". No entanto, o gateway utiliza o nome de "NT SERVICE\PBIEgwService" para as suas credenciais de conta "Iniciar sessão como". Por predefinição, o gateway tem permissões de "Iniciar sessão como um serviço" no computador onde instalou o gateway. A conta de serviço do Windows para o gateway difere, normalmente, a conta que utiliza para ligar a origens de dados no local e de trabalho conta escolar ou profissional que utilizar para iniciar sessão para serviços cloud.

Para poder criar e manter o gateway no portal do Azure, esta conta de serviço do Windows tem de ter, pelo menos, **contribuinte** permissões. Para verificar estas permissões, consulte [gerir o acesso com RBAC e o portal do Azure](../role-based-access-control/role-assignments-portal.md). 

<a name="restart-gateway"></a>

## <a name="restart-gateway"></a>Reiniciar o gateway

O gateway de dados é executado como um serviço de janela, assim como qualquer outro serviço do Windows, pode iniciar e parar o gateway de várias formas. Por exemplo, pode abrir uma linha de comandos com permissões elevadas no computador onde o gateway está em execução e execute o comando:

* Para parar o serviço, execute este comando:
  
  `net stop PBIEgwService`

* Para iniciar o serviço, execute este comando:
  
  `net start PBIEgwService`

## <a name="tenant-level-administration"></a>Administração de nível de inquilino 

Atualmente, não há um local único onde os administradores de inquilinos podem gerir todos os gateways que outros utilizadores tem instalado e configurado. Se for um administrador inquilino, pode querer ter os utilizadores na sua organização, adicioná-lo como um administrador para cada gateway que instalarem. Dessa forma, pode gerir todos os gateways na sua organização através da página de definições do Gateway ou através de [comandos do PowerShell](https://docs.microsoft.com/power-bi/service-gateway-high-availability-clusters#powershell-support-for-gateway-clusters). 

<a name="gateway-cloud-service"></a>

## <a name="how-does-the-gateway-work"></a>Como funciona o gateway?

O gateway de dados facilita a comunicação rápida e segura entre a aplicação lógica, o serviço de nuvem de gateway e sua origem de dados no local. Serviço cloud do gateway encripta e armazena as suas credenciais de origem de dados e detalhes do gateway. O serviço também encaminha consultas e seus resultados entre a aplicação lógica, o gateway de dados no local e sua origem de dados no local. 

O gateway funciona com firewalls e usa apenas as ligações de saída. Todo o tráfego de origem como proteger o tráfego de saída do agente do gateway. O gateway reencaminha dados a partir de origens no local em canais encriptados através do Azure Service Bus. Do service bus cria um canal entre o gateway e o serviço de chamada, mas não armazena quaisquer dados. Todos os dados que viaja através do gateway são encriptados.

![diagram-for-on-premises-data-gateway-flow](./media/logic-apps-gateway-install/how-on-premises-data-gateway-works-flow-diagram.png)

Estes passos descrevem o que acontece quando um utilizador na cloud interage com um elemento que está ligado à sua origem de dados no local:

1. O serviço de nuvem de gateway cria uma consulta, juntamente com as credenciais encriptadas para a origem de dados e envia a consulta para a fila para o gateway processar.

2. Serviço cloud do gateway analisa a consulta e envia o pedido para o Azure Service Bus.

3. O gateway de dados no local consulta o Azure Service Bus para pedidos pendentes.

4. O gateway obtém a consulta, desencripta as credenciais e liga-se à origem de dados com essas credenciais.

5. O gateway envia a consulta à origem de dados para execução.

6. Os resultados são enviados da origem de dados para o gateway e, em seguida, para o serviço cloud do gateway. Serviço cloud do gateway, em seguida, utiliza os resultados.

<a name="faq"></a>

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="general"></a>Geral

**Q**: É necessário um gateway para origens de dados na cloud, como a base de dados do Azure SQL? <br/>
**A**: Não, o gateway se liga a origens de dados no local apenas.

**Q**: O gateway tem de estar instalado no mesmo computador que a origem de dados? <br/>
**A**: Não, o gateway se liga à origem de dados com as informações de ligação fornecida. Considere o gateway como uma aplicação de cliente nesse sentido. O gateway precisa apenas a capacidade de ligar para o nome do servidor que foi fornecido.

<a name="why-azure-work-school-account"></a>

**Q**: Por que motivo deve utilizar uma conta escolar ou profissional para iniciar sessão? <br/>
**A**: Só pode utilizar uma conta escolar ou profissional ao instalar o gateway de dados no local. Sua conta de início de sessão é armazenada num inquilino gerido pelo Azure Active Directory (Azure AD). Normalmente, o nome de principal de utilizador (UPN) da sua conta do Azure AD corresponde ao endereço de e-mail.

**Q**: Onde estão armazenadas as minhas credenciais? <br/>
**A**: As credenciais que introduzir para uma origem de dados são encriptadas e armazenadas no serviço cloud do gateway. As credenciais são desencriptadas no gateway de dados no local.

**Q**: Existem requisitos de largura de banda de rede? <br/>
**A**: Verifique se a ligação de rede tem bom débito. Cada ambiente é diferente e a quantidade de dados enviados pode afetar os resultados. Para garantir um nível de débito entre a origem de dados no local e dos datacenters do Azure, experimente [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/). Para ajudar a medir o débito, experimente uma ferramenta externa, como o Azure Speed Test.

**Q**: O que é a latência de execução de consultas para uma origem de dados do gateway? O que é a melhor arquitetura? <br/>
**A**: Para reduzir a latência de rede, instale o gateway como próximo da origem de dados quanto possível. Se pode instalar o gateway na origem de dados real, este proximidade minimiza a latência introduzida. Além disso, considere a proximidade aos datacenters do Azure. Por exemplo, se o serviço utiliza o datacenter E.U.A. oeste e tiver o SQL Server alojado numa VM do Azure, em seguida, é aconselhável a VM do Azure na região E.U.A. oeste demasiado. Este proximidade minimiza a latência e evita custos de saída na VM do Azure.

**Q**: Como os resultados são enviados para a cloud? <br/>
**A**: Os resultados são enviados através do Azure Service Bus.

**Q**: Existem ligações de entrada para o gateway da cloud? <br/>
**A**: Não, o gateway utiliza ligações de saída para o Azure Service Bus.

**Q**: E se bloquear as ligações de saída? O que é necessário abrir? <br/>
**A**: Ver as portas e os anfitriões que o gateway utiliza.

**Q**: O que é o serviço real do Windows chamado? <br/>
**A**: No separador de serviços no Gerenciador de tarefas, o nome do serviço é "PBIEgwService" ou o serviço de Gateway do Power BI Enterprise. Na consola de serviços, o nome do serviço é o "serviço de gateway de dados no local". O serviço do Windows utiliza "NT SERVICE\PBIEgwService" como o SID de serviço (SSID).

**Q**: O serviço Windows do gateway pode ser executado com uma conta do Azure Active Directory? <br/>
**A**: Não, o serviço do Windows tem de ter uma conta Windows válida.

### <a name="disaster-recovery"></a>Recuperação após desastre

**Q**: Que opções estão disponíveis para recuperação após desastre? <br/>
**A**: Pode utilizar a chave de recuperação para restaurar ou mover um gateway. Ao instalar o gateway, especifique a chave de recuperação.

**Q**: Qual é a vantagem da chave de recuperação? <br/>
**A**: A chave de recuperação fornece uma forma de migrar ou recuperar as definições do gateway após um desastre.

## <a name="troubleshooting"></a>Resolução de problemas

Esta secção aborda alguns problemas comuns que poderá ser necessário ao configurar e utilizar o gateway de dados no local.

**Q**: Por que falha minha instalação de gateway? <br/>
**A**: Este problema pode acontecer se o software de antivírus no computador de destino está desatualizado. Pode atualizar o software de antivírus ou desativar o software de antivírus, mas apenas durante a instalação de gateway e, em seguida, ative novamente o software.

**Q**: Por que motivo não vejo minha instalação de gateway ao criar o recurso de gateway no Azure? <br/>
**A**: Este problema pode ocorrer por esses motivos:

* A instalação do gateway já esteja registrada e solicitada por outro recurso de gateway no Azure. Instalações de gateways não são apresentados na lista de instâncias depois de recursos de gateway são criados para eles.
Para verificar os registos de gateway no portal do Azure, reveja todos os recursos do Azure com o **Gateways de dados no local** escreva para *todos os* subscrições do Azure. 

* A identidade do Azure AD para a pessoa que instalar o gateway é diferente da pessoa que iniciou sessão no portal do Azure. Verifique que tem sessão iniciada com a mesma identidade que instalar o gateway.

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

**Q**: Onde estão os registos do gateway? <br/>
**A**: Consulte a [ **registos** seção](#logs) mais adiante neste artigo.

**Q**: Como posso ver que consultas estão a ser enviadas para a origem de dados no local? <br/>
**A**: Pode ativar o rastreio de consulta, que inclui as consultas que são enviadas. Não se esqueça de alterar a consulta rastreio de volta para o valor original quando terminado a resolução de problemas. Deixar o rastreio de consulta ativado cria registos maiores.

Pode também ver as ferramentas que a sua origem de dados tem para rastreio de consultas. Por exemplo, pode usar eventos expandidos ou o SQL Profiler para SQL Server e do Analysis Services.

### <a name="outdated-gateway-version"></a>Versão do gateway desatualizado

Muitos problemas podem surgir quando a versão do gateway torna-se desatualizados. Como uma prática recomendada geral, certifique-se de que tem a versão mais recente. Se não tiver atualizado o gateway durante um mês ou mais tempo, poderá considerar instalar a versão mais recente do gateway e veja se pode reproduzir o problema.

### <a name="error-failed-to-add-user-to-group--2147463168-pbiegwservice-performance-log-users"></a>Erro: Falha ao adicionar utilizador ao grupo. (-2147463168 PBIEgwService Performance Log Users)

Poderá receber este erro se tentar instalar o gateway num controlador de domínio, que não é suportado. Certifique-se de que implementar o gateway num computador que não seja um controlador de domínio.

<a name="logs"></a>

### <a name="logs"></a>Registos

Para ajudar a resolver problemas, sempre comece ao recolher e rever os registos do gateway. Existem várias formas para recolher os registos, mas a opção mais simples, depois de instalar o gateway é através da interface do usuário do instalador do gateway. 

1. No seu computador, abra o instalador do gateway de dados no local.
2. No menu da esquerda, selecione **diagnóstico**.
3. Sob **registos do Gateway**, selecione **exportar registos**.

   ![Exportar registos do instalador do gateway](./media/logic-apps-gateway-install/export-logs.png)

Seguem-se outros locais onde pode encontrar vários registos:

| Tipo de registo | Location | 
|----------|----------| 
| **Registos do instalador** | %localappdata%\Temp\On-premises_data_gateway_ <*AAAAMMDD*>. <*número*>. log | 
| **Registos de configuração** | C:\Users\<*nome de utilizador*> \AppData\Local\Microsoft\On-premises dados gateway\GatewayConfigurator <*AAAAMMDD*>. <*número*>. registo | 
| **Registos de serviço de gateway empresarial** | C:\Users\PBIEgwService\AppData\Local\Microsoft\On-Premises dados gateway\Gateway <*AAAAMMDD*>. <*número*>. log | 
||| 

**Registos de eventos**

Para localizar os registos de eventos para o gateway, siga estes passos:

1. No computador com a instalação do gateway, abra a **Visualizador de eventos**. 
2. Expanda **Visualizador de eventos (Local)**  > **registos de serviços e aplicações**. 
3. Selecione **serviço de gateway de dados no local**.

   ![Ver registos de eventos para o gateway](./media/logic-apps-gateway-install/event-viewer.png)

### <a name="review-slow-query-performance"></a>Desempenho de consulta lenta de revisão

Se encontrar consultas abrandar através do gateway, pode ativar o registo adicional que produz consultas e respetivas durações. Estes registos podem ajudá-lo a encontrar as consultas são lentas ou execução longa. Para otimizar o desempenho de consulta, poderá ter de modificar a sua origem de dados, por exemplo, ajuste de índices para consultas do SQL Server.

Para determinar a duração de uma consulta, siga estes passos:

1. Navegue para a mesma localização que o cliente de gateway, que normalmente pode ser encontrado aqui: ```C:\Program Files\On-premises data gateway```

   Caso contrário, para encontrar a localização do cliente, abra a consola de serviços no mesmo computador, encontre **serviço de gateway de dados no local**e ver o **caminho para o executável** propriedade.

2. Abrir e editar estes ficheiros de configuração, conforme descrito:

   * **Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config**

     Nesse arquivo, alterar os **EmitQueryTraces** partir da **false** para **verdadeiro** para que o gateway pode registar as consultas enviadas do gateway para uma origem de dados:

     ```html
     <setting name="EmitQueryTraces" serializeAs="String">
        <value>true</value>
     </setting>
     ```

     > [!IMPORTANT]
     > Ativando a definição de EmitQueryTraces poderá aumentar significativamente o tamanho do registo com base na utilização do gateway. Depois de concluir a rever os registos, certificar-se de que repõe EmitQueryTraces para **false** mais uma vez, em vez de deixar esta definição na longo prazo.

   * **Microsoft.PowerBI.DataMovement.Pipeline.Diagnostics.dll.config**

     Para ter o seu gateway registar as entradas verbosas, incluindo as entradas que mostram a duração, altere a **TracingVerbosity** partir da **4** para **5** ao realizar qualquer passo: 

     * No arquivo de configuração, altere a **TracingVerbosity** partir da **4** para **5** 

       ```html
       <setting name="TracingVerbosity" serializeAs="String">
          <value>5</value>
       </setting>
       ```

     * Abra o instalador do gateway, selecione **diagnóstico**, ative **registo adicional**e, em seguida, escolha **aplicar**:

       ![Ativar o registo adicional](./media/logic-apps-gateway-install/turn-on-additional-logging.png)

     > [!IMPORTANT]
     > Ativando a definição de TracingVerbosity poderá aumentar significativamente o tamanho do registo com base na utilização do gateway. Depois de concluir a rever os registos, certifique-se de que desative **registo adicional** no instalador do gateway ou repor TracingVerbosity para **4** novamente no ficheiro de configuração, em vez de deixar esta definição no a longo prazo.

3. Para localizar a duração de uma consulta, siga estes passos:

   1. [Exportar](#logs) e abra o registo do gateway.

   2. Para encontrar uma consulta, procure um tipo de atividade, por exemplo: 

      | Tipo de atividade | Descrição | 
      |---------------|-------------| 
      | MGEQ | Consultas que são executadas através de ADO.NET. | 
      | MGEO | Consultas que são executadas através de OLEDB. | 
      | MGEM | Consultas que executam a partir do motor de aplicação híbrida. | 
      ||| 

   3. Tenha em atenção o segundo GUID, o que é o pedido de ID.

   4. Continue a pesquisar para o tipo de atividade até encontrar uma entrada com o nome "FireActivityCompletedSuccessfullyEvent" que tem uma duração em milissegundos. 
   Confirme que a entrada tem o mesmo ID do pedido, por exemplo:

      ```text 
      DM.EnterpriseGateway Verbose: 0 : 2016-09-26T23:08:56.7940067Z DM.EnterpriseGateway    baf40f21-2eb4-4af1-9c59-0950ef11ec4a    5f99f566-106d-c8ac-c864-c0808c41a606    MGEQ    21f96cc4-7496-bfdd-748c-b4915cb4b70c    B8DFCF12 [DM.Pipeline.Common.TracingTelemetryService] Event: FireActivityCompletedSuccessfullyEvent (duration=5004)
      ```

      > [!NOTE] 
      > A entrada "FireActivityCompletedSuccessfullyEvent" é uma entrada verbosa e não está conectada, a menos que a definição de "TracingVerbosity" está no nível 5.

### <a name="trace-traffic-with-fiddler"></a>Tráfego de rastreio com o Fiddler

[Fiddler](https://www.telerik.com/fiddler) é uma ferramenta gratuita da Telerik que monitoriza o tráfego HTTP. Pode rever este tráfego com o serviço Power BI do computador cliente. Este serviço poderá mostrar erros e outras informações relacionadas.

## <a name="next-steps"></a>Passos Seguintes
    
* [Ligar a dados no local a partir de aplicações lógicas](../logic-apps/logic-apps-gateway-connection.md)
* [Recursos de integração do Enterprise](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Conectores para o Azure Logic Apps](../connectors/apis-list.md)
