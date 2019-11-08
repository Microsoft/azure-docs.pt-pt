---
title: Instalar o gateway de dados local-aplicativos lógicos do Azure
description: Antes de poder acessar dados locais de aplicativos lógicos do Azure, baixe e instale o gateway de dados local
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: arthii, LADocs
ms.topic: article
ms.date: 11/06/2019
ms.openlocfilehash: a8deb7933bb19745bbe4c3b3a209c19c9cc712bd
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73796315"
---
# <a name="install-on-premises-data-gateway-for-azure-logic-apps"></a>Instalar o gateway de dados local para aplicativos lógicos do Azure

Antes de poder [se conectar a fontes de dados locais de aplicativos lógicos do Azure](../logic-apps/logic-apps-gateway-connection.md), baixe e instale o [Gateway de dados](https://aka.ms/on-premises-data-gateway-installer) local em um computador local. O gateway funciona como uma ponte que fornece transferência rápida de dados e criptografia entre fontes de dados locais e seus aplicativos lógicos. Você pode usar a mesma instalação de gateway com outros serviços de nuvem, como Power BI, Power mate, Power apps e Azure Analysis Services. Para obter informações sobre como usar o gateway com esses serviços, consulte estes artigos:

* [Gateway de dados local do Microsoft Power BI](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem/)
* [Gateway de dados local do Microsoft Power apps](https://powerapps.microsoft.com/tutorials/gateway-management/)
* [Automatizar o gateway de dados local de energia](https://flow.microsoft.com/documentation/gateway-manage/)
* [Azure Analysis Services Gateway de dados local](../analysis-services/analysis-services-gateway.md)

Este artigo mostra como baixar, instalar e configurar seu gateway de dados local para que você possa acessar fontes de dados locais de aplicativos lógicos do Azure. Você também pode saber mais sobre [como o gateway de dados funciona](#gateway-cloud-service) mais adiante neste tópico. Para obter mais informações sobre o gateway, consulte [o que é um gateway local](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem)?

<a name="requirements"></a>

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta e subscrição do Azure. Se você não tiver uma conta do Azure com uma assinatura, [Inscreva-se para obter uma conta gratuita do Azure](https://azure.microsoft.com/free/).

  * Sua conta do Azure deve pertencer a um único [locatário ou diretório do Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md#terminology). Você deve usar a mesma conta do Azure para instalar e administrar o gateway no computador local.

  * Durante a instalação do gateway, você entra com sua conta do Azure, que vincula a instalação do seu gateway à sua conta do Azure e somente essa conta. Posteriormente, no portal do Azure, você deve usar a mesma conta do Azure e o locatário do Azure AD ao criar um recurso de gateway do Azure que registra e declara a instalação do seu gateway. Em aplicativos lógicos do Azure, gatilhos e ações locais usam o recurso de gateway para se conectar a fontes de dados locais.

    > [!NOTE]
    > Você pode vincular apenas uma instalação de gateway e um recurso de gateway do Azure entre si. Não é possível vincular a mesma instalação de gateway a várias contas do Azure ou recursos de gateway do Azure. No entanto, uma conta do Azure pode ser vinculada a várias instalações de gateway e recursos de gateway do Azure. Em um gatilho ou ação local, você pode selecionar entre suas várias assinaturas do Azure e, em seguida, selecionar um recurso de gateway associado.

  * Você precisa entrar com uma conta corporativa ou de estudante, também conhecida como uma conta da *organização* , que se parece com `username@contoso.com`. Você não pode usar contas do Azure B2B (convidado) ou contas pessoais da Microsoft, como @hotmail.com ou @outlook.com.

    > [!TIP]
    > Se você se inscreveu para uma oferta do Office 365 e não forneceu seu endereço de email de trabalho, seu endereço pode ser semelhante a `username@domain.onmicrosoft.com`. Sua conta é armazenada em um locatário em um Azure Active Directory (Azure AD). Na maioria dos casos, o nome principal do usuário (UPN) para sua conta do Azure AD é o mesmo que seu endereço de email.
    >
    > Para usar uma [assinatura padrão do Visual Studio](https://visualstudio.microsoft.com/vs/pricing/) que esteja vinculada a um conta Microsoft, primeiro [crie um locatário no Azure ad](../active-directory/develop/quickstart-create-new-tenant.md) ou use o diretório padrão. Adicione um usuário com uma senha ao diretório e conceda a esse usuário acesso à sua assinatura do Azure. Em seguida, você pode entrar durante a instalação do gateway com esse nome de usuário e senha.

* Aqui estão os requisitos para seu computador local:

  **Requisitos mínimos**

  * .NET Framework 4.7.2
  * versão de 64 bits do Windows 7 ou Windows Server 2008 R2 (ou posterior)

  **Requisitos recomendados**

  * CPU de 8 núcleos
  * 8 GB de memória
  * versão de 64 bits do Windows Server 2012 R2 ou posterior
  * Armazenamento SSD (unidade de estado sólido) para o spooling

  > [!NOTE]
  > O gateway não dá suporte ao Windows Server Core.

* **Considerações relacionadas**

  * Você pode instalar o gateway de dados local somente em um computador local, não em um controlador de domínio. No entanto, você não precisa instalar o gateway no mesmo computador que a fonte de dados. Você precisa de apenas um gateway para todas as suas fontes de dados, portanto, não é necessário instalar o gateway para cada fonte de dados.

    > [!TIP]
    > Para minimizar a latência, você pode instalar o gateway o mais próximo possível da fonte de dados ou no mesmo computador, supondo que você tenha permissões.

  * Instale o gateway em um computador que esteja em uma rede com fio, conectado à Internet, sempre ligado e não vá para o estado de suspensão. Caso contrário, o gateway não pode ser executado e o desempenho pode ser afetado em uma rede sem fio.

  * Se você planeja usar a autenticação do Windows, certifique-se de instalar o gateway em um computador que seja membro do mesmo ambiente de Active Directory que suas fontes de dados.

  * A região que você selecionar para a instalação do gateway é o mesmo local que você deve selecionar quando você cria posteriormente o recurso de gateway do Azure para seu aplicativo lógico. Por padrão, essa região é o mesmo local que o seu locatário do Azure AD que gerencia sua conta do Azure. No entanto, você pode alterar o local durante a instalação do gateway.

  * O gateway tem dois modos: modo padrão e modo pessoal, que se aplica somente a Power BI. Você não pode ter mais de um gateway em execução no mesmo modo no mesmo computador.

  * O aplicativo lógico do Azure dá suporte a operações de leitura e gravação por meio do gateway. No entanto, essas operações têm [limites de seu tamanho de carga](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem#considerations).

<a name="install-gateway"></a>

## <a name="install-data-gateway"></a>Instalar o gateway de dados

1. [Baixe e execute o instalador do gateway em um computador local](https://aka.ms/on-premises-data-gateway-installer).

1. Depois que o instalador for aberto, selecione **Avançar**.

   ![Tela de introdução para o instalador do gateway](./media/logic-apps-gateway-install/gateway-intro-screen.png)

1. Selecione **Gateway de dados local (recomendado)** , que é o modo padrão e, em seguida, selecione **Avançar**.

   ![Selecione o modo de execução para o gateway de dados](./media/logic-apps-gateway-install/select-gateway-running-mode.png)

1. Examine os requisitos mínimos, mantenha o caminho de instalação padrão, aceite os termos de uso e, em seguida, selecione **instalar**.

   ![Revise os requisitos e aceite os termos de uso](./media/logic-apps-gateway-install/review-and-accept-terms-of-use.png)

1. Depois que o gateway for instalado com êxito, forneça o endereço de email para sua conta do Azure e, em seguida, selecione **entrar**, por exemplo:

   ![Entrar com uma conta corporativa ou de estudante](./media/logic-apps-gateway-install/sign-in-gateway-install.png)

   A instalação do gateway pode ser vinculada a apenas uma conta do Azure.

1. Selecione **registrar um novo gateway neste computador** > **Avançar**. Essa etapa registra a instalação do gateway com o [serviço de nuvem do gateway](#gateway-cloud-service).

   ![Registrar gateway no computador local](./media/logic-apps-gateway-install/register-gateway-local-computer.png)

1. Forneça essas informações para a instalação do gateway:

   * Um nome de gateway que é exclusivo em seu locatário do Azure AD
   * A chave de recuperação, que deve ter pelo menos oito caracteres, que você deseja usar
   * Confirmação para sua chave de recuperação

   ![Fornecer informações para a instalação do gateway](./media/logic-apps-gateway-install/gateway-name-recovery-key.png)

   > [!IMPORTANT]
   > Salve e mantenha sua chave de recuperação em um local seguro. Você precisará dessa chave se quiser alterar o local, mover, recuperar ou assumir uma instalação de gateway.

   Observe a opção para **Adicionar a um cluster de gateway existente**, que você seleciona ao instalar gateways adicionais para [cenários de alta disponibilidade](#high-availability).

1. Verifique a região para o serviço de nuvem do gateway e o [barramento de serviço do Azure](https://azure.microsoft.com/services/service-bus/) que é usado pela instalação do seu gateway. Por padrão, essa região é o mesmo local que o locatário do Azure AD para sua conta do Azure.

   ![Confirmar região para serviço de gateway e barramento de serviço](./media/logic-apps-gateway-install/confirm-gateway-region.png)

1. Para aceitar a região padrão, selecione **Configurar**. No entanto, se a região padrão não for aquela mais próxima de você, você poderá alterar a região.

   *Por que alterar a região para a instalação do gateway?*

   Por exemplo, para reduzir a latência, você pode alterar a região do gateway para a mesma região que seu aplicativo lógico. Ou, você pode selecionar a região mais próxima de sua fonte de dados local. O *recurso de gateway no Azure* e seu aplicativo lógico podem ter locais diferentes.

   1. Ao lado da região atual, selecione **alterar região**.

      ![Alterar a região do gateway atual](./media/logic-apps-gateway-install/change-gateway-service-region.png)

   1. Na página seguinte, abra a lista **selecionar região** , selecione a região desejada e selecione **concluído**.

      ![Selecionar outra região para o serviço de gateway](./media/logic-apps-gateway-install/select-region-gateway-install.png)

1. Examine as informações na janela de confirmação final. Este exemplo usa a mesma conta para aplicativos lógicos, Power BI, Power apps e automatização de energia, para que o gateway esteja disponível para todos esses serviços. Quando estiver pronto, selecione **fechar**.

   ![Confirmar informações do gateway de dados](./media/logic-apps-gateway-install/finished-gateway-default-location.png)

1. Agora, [crie o recurso do Azure para a instalação do gateway](../logic-apps/logic-apps-gateway-connection.md).

## <a name="check-or-adjust-communication-settings"></a>Verificar ou ajustar as configurações de comunicação

O gateway de dados local depende do [barramento de serviço do Azure](../service-bus-messaging/service-bus-messaging-overview.md) para conectividade de nuvem e estabelece as conexões de saída correspondentes à região do Azure associada do gateway. Se o seu ambiente de trabalho exigir que o tráfego passe por um proxy ou firewall para acessar a Internet, essa restrição poderá impedir que o gateway de dados local se conecte ao serviço de nuvem do gateway e ao barramento de serviço do Azure. O gateway tem várias configurações de comunicação que você pode ajustar. Para obter mais informações, consulte estes tópicos:

* [Ajustar as configurações de comunicação para o gateway de dados local](https://docs.microsoft.com/data-integration/gateway/service-gateway-communication)
* [Definir configurações de proxy para o gateway de dados local](https://docs.microsoft.com/data-integration/gateway/service-gateway-proxy)

<a name="high-availability"></a>

## <a name="high-availability-support"></a>Suporte de alta disponibilidade

Para evitar pontos únicos de falha para acesso a dados local, você pode ter várias instalações de gateway (somente no modo padrão) com cada um em um computador diferente e configurá-las como um cluster ou grupo. Dessa forma, se o gateway primário não estiver disponível, as solicitações de dados serão roteadas para o segundo gateway e assim por diante. Como você pode instalar apenas um gateway padrão em um computador, você deve instalar cada gateway adicional que está no cluster em um computador diferente. Todos os conectores que trabalham com o gateway de dados local dão suporte à alta disponibilidade.

* Você já deve ter pelo menos uma instalação de gateway com a mesma conta do Azure que o gateway primário e a chave de recuperação para essa instalação.

* O gateway primário deve estar executando a atualização do gateway de novembro de 2017 ou posterior.

Depois de configurar o gateway primário, quando você for instalar outro gateway, selecione **Adicionar a um cluster de gateway existente**, selecione o gateway primário, que é o primeiro gateway que você instalou e forneça a chave de recuperação para esse gateway. Para obter mais informações, consulte [clusters de alta disponibilidade para o gateway de dados local](https://docs.microsoft.com/data-integration/gateway/service-gateway-install#add-another-gateway-to-create-a-cluster).

<a name="update-gateway-installation"></a>

## <a name="change-location-migrate-restore-or-take-over-existing-gateway"></a>Alterar local, migrar, restaurar ou assumir o gateway existente

Se for necessário alterar o local do gateway, mover a instalação do gateway para um novo computador, recuperar um gateway danificado ou apropriar-se de um gateway existente, você precisará da chave de recuperação que foi fornecida durante a instalação do gateway.

1. Execute o instalador do gateway no computador que tem o gateway existente. Se você não tiver o instalador do gateway mais recente, [Baixe a versão mais recente do gateway](https://aka.ms/on-premises-data-gateway-installer).

   > [!NOTE]
   > Antes de restaurar o gateway no computador que tem a instalação do gateway original, você deve primeiro desinstalar o gateway nesse computador. Essa ação desconecta o gateway original.
   > Se você remover ou excluir um cluster de gateway para qualquer serviço de nuvem, não poderá restaurar esse cluster.

1. Depois que o instalador for aberto, entre com a mesma conta do Azure que foi usada para instalar o gateway.

1. Selecione **migrar, restaurar ou tomada um gateway existente** > **Avançar**, por exemplo:

   ![Selecione "migrar, restaurar ou tomada um gateway existente"](./media/logic-apps-gateway-install/migrate-recover-take-over-gateway.png)

1. Selecione entre os clusters e gateways disponíveis e insira a chave de recuperação para o gateway selecionado, por exemplo:

   ![Selecionar gateway e fornecer chave de recuperação](./media/logic-apps-gateway-install/select-existing-gateway.png)

1. Para alterar a região, selecione **alterar região**e selecione a nova região.

1. Quando estiver pronto, selecione **Configurar** para que você possa concluir a tarefa.

## <a name="tenant-level-administration"></a>Administração no nível do locatário

Para obter visibilidade de todos os gateways de dados locais em um locatário do Azure AD, os administradores globais nesse locatário podem entrar no centro de [Administração do Power Platform](https://powerplatform.microsoft.com) como administrador de locatários e selecionar a opção de **gateways de dados** . Para obter mais informações, consulte [administração em nível de locatário para o gateway de dados local](https://docs.microsoft.com/data-integration/gateway/service-gateway-tenant-level-admin).

<a name="restart-gateway"></a>

## <a name="restart-gateway"></a>Reiniciar gateway

Por padrão, a instalação do gateway no computador local é executada como uma conta de serviço do Windows denominada "serviço de gateway de dados local". No entanto, a instalação do gateway usa o nome do `NT SERVICE\PBIEgwService` para suas credenciais de conta "fazer logon como" e tem permissões para "fazer logon como um serviço".

> [!NOTE]
> Sua conta de serviço do Windows difere da conta usada para conectar-se a fontes de dados locais e da conta do Azure que você usa ao entrar nos serviços de nuvem.

Como qualquer outro serviço do Windows, você pode iniciar e parar o gateway de várias maneiras. Para obter mais informações, consulte [reiniciar um gateway de dados local](https://docs.microsoft.com/data-integration/gateway/service-gateway-restart).

<a name="gateway-cloud-service"></a>

## <a name="how-the-gateway-works"></a>Como funciona o gateway

Os usuários em sua organização podem acessar dados locais para os quais eles já têm acesso autorizado. No entanto, antes que esses usuários possam se conectar à sua fonte de dados local, você precisa instalar e configurar um gateway de dados local. Normalmente, um administrador é a pessoa que instala e configura um gateway. Essas ações podem exigir permissões de administrador do servidor ou conhecimento especial sobre seus servidores locais.

O gateway facilita a comunicação rápida e segura por trás das cenas de comunicação. Essa comunicação flui entre um usuário na nuvem, o serviço de nuvem do gateway e sua fonte de dados local. O serviço de nuvem do gateway criptografa e armazena suas credenciais de fonte de dados e detalhes do gateway. O serviço também roteia consultas e seus resultados entre o usuário, o gateway e sua fonte de dados local.

O gateway funciona com firewalls e usa apenas conexões de saída. Todo o tráfego é originado como tráfego de saída seguro do agente de gateway. O gateway retransmite dados de fontes locais em canais criptografados por meio do [barramento de serviço do Azure](../service-bus-messaging/service-bus-messaging-overview.md). Esse barramento de serviço cria um canal entre o gateway e o serviço de chamada, mas não armazena nenhum dado. Todos os dados que trafegam pelo gateway são criptografados.

![Arquitetura do gateway de dados local](./media/logic-apps-gateway-install/how-on-premises-data-gateway-works-flow-diagram.png)

> [!NOTE]
> Dependendo do serviço de nuvem, talvez seja necessário configurar uma fonte de dados para o gateway.

Estas etapas descrevem o que acontece quando você interage com um elemento que está conectado a uma fonte de dados local:

1. O serviço de nuvem cria uma consulta, juntamente com as credenciais criptografadas para a fonte de dados. Em seguida, o serviço envia a consulta e as credenciais para a fila do gateway para processamento.

1. O serviço de nuvem do gateway analisa a consulta e envia a solicitação para o barramento de serviço do Azure.

1. O barramento de serviço do Azure envia as solicitações pendentes para o gateway.

1. O gateway Obtém a consulta, descriptografa as credenciais e se conecta a uma ou mais fontes de dados com essas credenciais.

1. O gateway envia a consulta à fonte de dados para execução.

1. Os resultados são enviados da fonte de dados de volta para o gateway e, em seguida, para o serviço de nuvem do gateway. Em seguida, o serviço de nuvem do gateway usa os resultados.

### <a name="authentication-to-on-premises-data-sources"></a>Autenticação para fontes de dados locais

Uma credencial armazenada é usada para se conectar do gateway para fontes de dados locais. Independentemente do usuário, o gateway usa a credencial armazenada para se conectar. Pode haver exceções de autenticação para serviços específicos, como DirectQuery e LiveConnect para Analysis Services em Power BI.

### <a name="azure-active-directory-azure-ad"></a>Azure Active Directory (Azure AD)

Os serviços de nuvem da Microsoft usam o [Azure ad](../active-directory/fundamentals/active-directory-whatis.md) para autenticar usuários. Um locatário do Azure AD contém nomes de domínio e grupos de segurança. Normalmente, o endereço de email que você usa para entrar é o mesmo que o UPN (nome principal do usuário) da sua conta.

### <a name="what-is-my-upn"></a>Qual é o meu UPN?

Se você não for um administrador de domínio, talvez não saiba seu UPN. Para localizar o UPN para sua conta, execute o comando `whoami /upn` da sua estação de trabalho. Embora o resultado seja como um endereço de email, o resultado é o UPN para sua conta de domínio local.

### <a name="synchronize-an-on-premises-active-directory-with-azure-ad"></a>Sincronizar um Active Directory local com o Azure AD

O UPN para suas contas de Active Directory locais e contas do Azure AD deve ser o mesmo. Portanto, certifique-se de que cada conta de Active Directory local corresponde à sua conta do Azure AD. Os serviços de nuvem sabem apenas sobre contas no Azure AD. Portanto, você não precisa adicionar uma conta ao seu Active Directory local. Se a conta não existir no Azure AD, você não poderá usar essa conta.

Aqui estão as maneiras pelas quais você pode corresponder às suas contas de Active Directory locais com o Azure AD.

* Adicione contas manualmente ao Azure AD.

  Crie uma conta no portal do Azure ou no centro de administração do Microsoft 365. Verifique se o nome da conta corresponde ao UPN para a conta de Active Directory local.

* Sincronize contas locais para seu locatário do Azure AD usando a ferramenta Azure Active Directory Connect.

  A ferramenta de Azure AD Connect fornece opções de sincronização de diretório e configuração de autenticação. Essas opções incluem sincronização de hash de senha, autenticação de passagem e Federação. Se você não for um administrador de locatários ou um administrador de domínio local, entre em contato com o administrador de ti para obter Azure AD Connect configurado. Azure AD Connect garante que o UPN do Azure AD corresponda ao seu UPN Active Directory local. Essa correspondência ajudará se você estiver usando Analysis Services conexões dinâmicas com Power BI ou recursos de logon único (SSO).

  > [!NOTE]
  > A sincronização de contas com a ferramenta Azure AD Connect cria novas contas em seu locatário do Azure AD.

<a name="faq"></a>

## <a name="faq-and-troubleshooting"></a>Perguntas frequentes e solução de problemas

Para obter mais informações, consulte estes tópicos:

* [FAQ sobre o gateway de dados no local](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem-faq)
* [Solucionar problemas do gateway de dados local](https://docs.microsoft.com/data-integration/gateway/service-gateway-tshoot)
* [Monitorar e otimizar o desempenho do gateway](https://docs.microsoft.com/data-integration/gateway/service-gateway-performance)

## <a name="next-steps"></a>Passos seguintes

* [Conectar-se a dados locais de aplicativos lógicos](../logic-apps/logic-apps-gateway-connection.md)
* [Recursos de integração corporativa](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Conectores para Azure Logic Apps](../connectors/apis-list.md)
