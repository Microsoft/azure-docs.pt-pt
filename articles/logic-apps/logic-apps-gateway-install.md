---
title: Instalar um gateway de dados no local
description: Antes de poder aceder a dados nas instalações de Aplicações Da Lógica Azure, descarregue e instale o portal de dados no local
services: logic-apps
ms.suite: integration
ms.reviewer: arthii, logicappspm
ms.topic: article
ms.date: 12/05/2019
ms.openlocfilehash: f2f8b9f207993c49201d03d3d1fed3c5800e8780
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80673823"
---
# <a name="install-on-premises-data-gateway-for-azure-logic-apps"></a>Instalar o gateway de dados local para Azure Logic Apps

Antes de se [ligar a fontes de dados no local a partir de Aplicações Lógicas Do Azure,](../logic-apps/logic-apps-gateway-connection.md)descarregue e instale a [porta de dados no local](https://aka.ms/on-premises-data-gateway-installer) num computador local. O gateway funciona como uma ponte que fornece transferência rápida de dados e encriptação entre fontes de dados nas instalações e as suas aplicações lógicas. Pode utilizar a mesma instalação de gateway com outros serviços na nuvem, tais como Power BI, Power Automate, Power Apps e Azure Analysis Services. Para obter informações sobre como usar a porta de entrada com estes serviços, consulte estes artigos:

* [Microsoft Power Automatizar no local gateway de dados](/power-automate/gateway-reference)
* [Microsoft Power BI no local gateway de dados](/power-bi/service-gateway-onprem)
* [Microsoft Power Apps no local gateway de dados](/powerapps/maker/canvas-apps/gateway-reference)
* [Gateway de dados dos Serviços de Análise Azure no local](../analysis-services/analysis-services-gateway.md)

Este artigo mostra como descarregar, instalar e configurar a sua porta de dados no local para que possa aceder ao local de fontes de dados a partir de Aplicações Lógicas Azure. Também pode saber mais sobre [como o portal de dados funciona](#gateway-cloud-service) mais tarde neste tópico. Para mais informações sobre o portal, veja [o que é um portal no local?](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem) Para automatizar tarefas de instalação e gestão de gateways, visite a galeria PowerShell para os [cmdlets DataGateway PowerShell](https://www.powershellgallery.com/packages/DataGateway/3000.15.15).

<a name="requirements"></a>

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta e subscrição do Azure. Se não tiver uma conta Azure com uma subscrição, [inscreva-se numa conta Azure gratuita.](https://azure.microsoft.com/free/)

  * A sua conta Azure deve pertencer a um único [inquilino ou diretório Azure Ative Directory (Azure AD).](../active-directory/fundamentals/active-directory-whatis.md#terminology) Deve utilizar a mesma conta Azure para instalar e administrar o portal no seu computador local.

  * Durante a instalação do gateway, insere-se na sua conta Azure, que liga a instalação do gateway à sua conta Azure e apenas a essa conta. Mais tarde, no portal Azure, deve utilizar a mesma conta Azure e o inquilino Azure AD quando criar um recurso de gateway Azure que se registe e reclama a sua instalação de gateway. Nas Aplicações Lógicas Azure, os gatilhos e ações no local usam o recurso gateway para se conectarem a fontes de dados no local.

    > [!NOTE]
    > Só pode ligar uma instalação de gateway e um recurso azure gateway entre si. Não é possível ligar a mesma instalação de gateway a várias contas Azure ou recursos de gateway Azure. No entanto, uma conta Azure pode ligar-se a múltiplas instalações de gateway e recursos de gateway Azure. Num gatilho ou ação no local, pode selecionar entre as suas várias subscrições Azure e, em seguida, selecionar um recurso de gateway associado.

  * Você precisa assinar com uma conta de trabalho ou escola, também conhecida `username@contoso.com`como uma conta de *organização,* que parece . Não pode utilizar contas Azure B2B (hóspede) ou contas @hotmail.com pessoais da Microsoft, tais como ou @outlook.com.

    > [!TIP]
    > Se se inscreveu para uma oferta do Office 365 e não forneceu `username@domain.onmicrosoft.com`o seu endereço de e-mail de trabalho, o seu endereço pode parecer . A sua conta está armazenada dentro de um inquilino num Azure Ative Directory (Azure AD). Na maioria dos casos, o Nome Principal do Utilizador (UPN) para a sua conta Azure AD é o mesmo que o seu endereço de e-mail.
    >
    > Para utilizar uma [subscrição Visual Studio Standard](https://visualstudio.microsoft.com/vs/pricing/) que esteja ligada a uma conta Microsoft, crie primeiro [um inquilino em Azure AD](../active-directory/develop/quickstart-create-new-tenant.md) ou use o diretório padrão. Adicione um utilizador com uma palavra-passe ao diretório e, em seguida, dê a esse utilizador acesso à sua subscrição Azure. Em seguida, pode iniciar sessão durante a instalação do gateway com este nome de utilizador e senha.

* Aqui estão os requisitos para o seu computador local:

  **Requisitos mínimos**

  * .Quadro líquido 4.7.2
  * Versão de 64 bits do Windows 7 ou Windows Server 2008 R2 (ou posterior)

  **Requisitos recomendados**

  * CPU de 8 núcleos
  * Memória de 8 GB
  * Versão de 64 bits do Windows Server 2012 R2 ou posterior
  * Armazenamento de unidade de estado sólido (SSD) para bobinar

  > [!NOTE]
  > O portal não suporta o Núcleo do Servidor do Windows.

* **Considerações relacionadas**

  * Instale a porta de dados no local apenas num computador local, e não num controlador de domínio. Não tem de instalar o portal no mesmo computador que a sua fonte de dados. Só precisa de um portal para todas as suas fontes de dados, para que não precise de instalar o portal para cada fonte de dados.

    > [!TIP]
    > Para minimizar a latência, pode instalar o portal o mais próximo possível da sua fonte de dados, ou no mesmo computador, assumindo que tem permissões.

  * Instale a porta de entrada num computador que esteja numa rede com fios, ligada à internet, sempre ligada, e não durma. Caso contrário, o portal não pode funcionar, e o desempenho pode sofrer por uma rede sem fios.

  * Se pretender utilizar a autenticação do Windows, certifique-se de que instala o portal num computador que seja membro do mesmo ambiente de Diretório Ativo que as suas fontes de dados.

  * A região que seleciona para a instalação do gateway é a mesma localização que deve selecionar quando mais tarde criar o recurso De gateway Azure para a sua aplicação lógica. Por predefinição, esta região é a mesma localização que o seu inquilino Azure AD que gere a sua conta Azure. No entanto, pode alterar a localização durante a instalação do portal.

  * Se estiver a atualizar a instalação do gateway para a versão mais recente, desinstale primeiro o seu portal atual para uma experiência mais limpa.

  * O gateway tem dois modos: modo padrão e modo pessoal, que se aplica apenas ao Power BI. Não pode ter mais do que um portal a funcionar no mesmo modo no mesmo computador.

  * As Aplicações Lógicas Azure suportam as operações de leitura e escrita através do portal. No entanto, estas operações têm [limites para o seu tamanho](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem#considerations)de carga útil .

<a name="install-gateway"></a>

## <a name="install-data-gateway"></a>Instalar o gateway de dados

1. [Descarregue e execute o instalador de gateway num computador local](https://aka.ms/on-premises-data-gateway-installer).

1. Reveja os requisitos mínimos, mantenha o caminho de instalação predefinido, aceite os termos de utilização e, em seguida, **selecione Instalar**.

   ![Rever os requisitos e aceitar termos de utilização](./media/logic-apps-gateway-install/review-and-accept-terms-of-use.png)

1. Depois de o gateway instalar com sucesso, forneça o endereço de e-mail para a sua conta Azure e, em seguida, selecione **Iniciar sessão,** por exemplo:

   ![Inscreva-se com trabalho ou conta escolar](./media/logic-apps-gateway-install/sign-in-gateway-install.png)

   A instalação do gateway pode ligar-se a apenas uma conta Azure.

1. Selecione **Registar uma nova porta de entrada neste computador** > **Seguinte**. Este passo regista a instalação do gateway com o serviço de [nuvem](#gateway-cloud-service)gateway .

   ![Registe a porta de entrada no computador local](./media/logic-apps-gateway-install/register-gateway-local-computer.png)

1. Forneça estas informações para a sua instalação de gateway:

   * Um nome de gateway que é único em todo o seu inquilino Azure AD
   * A chave de recuperação, que deve ter pelo menos oito caracteres, que você quer usar
   * Confirmação da sua chave de recuperação

   ![Fornecer informações para a instalação de gateway](./media/logic-apps-gateway-install/gateway-name-recovery-key.png)

   > [!IMPORTANT]
   > Guarde e mantenha a chave de recuperação num local seguro. Você precisa desta chave se alguma vez quiser mudar a localização, mover-se, recuperar ou assumir uma instalação de gateway.

   Tenha em anotaaada a opção de adicionar a um cluster de **gateway existente,** que seleciona quando instala gateways adicionais para [cenários de alta disponibilidade](#high-availability).

1. Consulte a região para ver se há serviço de cloud gateway e [azure Service Bus](https://azure.microsoft.com/services/service-bus/) que é usado pela instalação do seu portal. Por predefinição, esta região é a mesma localização que o inquilino da AD Azure para a sua conta Azure.

   ![Confirme a região para o serviço de gateway e autocarro de serviço](./media/logic-apps-gateway-install/confirm-gateway-region.png)

1. Para aceitar a região predefinida, **selecione Configurar**. No entanto, se a região padrão não for a que está mais próxima de si, pode mudar a região.

   *Por que mudar a região para a sua instalação de gateway?*

   Por exemplo, para reduzir a latência, pode mudar a região do seu portal para a mesma região que a sua aplicação lógica. Ou, pode selecionar a região mais próxima da sua fonte de dados no local. O seu *recurso gateway em Azure* e a sua aplicação lógica podem ter diferentes localizações.

   1. Junto à região atual, selecione **Região de Mudança.**

      ![Alterar a atual região de gateway](./media/logic-apps-gateway-install/change-gateway-service-region.png)

   1. Na página seguinte, abra a lista **Select Region,** selecione a região que deseja e selecione **Done**.

      ![Selecione outra região para o serviço gateway](./media/logic-apps-gateway-install/select-region-gateway-install.png)

1. Reveja a informação na janela de confirmação final. Este exemplo utiliza a mesma conta para Aplicações Lógicas, Power BI, Power Apps e Power Automate, pelo que o portal está disponível para todos estes serviços. Quando estiver pronto, selecione **Fechar**.

   ![Confirmar informações sobre gateways de dados](./media/logic-apps-gateway-install/finished-gateway-default-location.png)

1. Agora [crie o recurso Azure para a sua instalação](../logic-apps/logic-apps-gateway-connection.md)de gateway.

## <a name="check-or-adjust-communication-settings"></a>Verificar ou ajustar as definições de comunicação

A porta de dados no local depende do [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) para conectividade na nuvem e estabelece as correspondentes ligações de saída à região azure associada do gateway. Se o seu ambiente de trabalho exigir que o tráfego passe por um proxy ou firewall para aceder à internet, esta restrição pode impedir que a porta de dados no local se ligue ao serviço de nuvem gateway e ao Azure Service Bus. O portal tem várias definições de comunicação, que pode ajustar. Para obter mais informações, veja estes tópicos:

* [Ajuste as definições de comunicação para o gateway de dados no local](https://docs.microsoft.com/data-integration/gateway/service-gateway-communication)
* [Configurar as definições de proxy do gateway de dados no local](https://docs.microsoft.com/data-integration/gateway/service-gateway-proxy)

<a name="high-availability"></a>

## <a name="high-availability-support"></a>Suporte de alta disponibilidade

Para evitar pontos únicos de falha no acesso a dados no local, pode ter múltiplas instalações de gateway (apenas modo padrão) com cada uma num computador diferente, e configurar-as como um cluster ou grupo. Dessa forma, se a porta principal não estiver disponível, os pedidos de dados são encaminhados para a segunda porta de entrada, e assim por diante. Como só pode instalar um portal padrão num computador, tem de instalar cada gateway adicional que esteja no cluster num computador diferente. Todos os conectores que trabalham com o gateway de dados no local suportam alta disponibilidade.

* Já deve ter pelo menos uma instalação de gateway com a mesma conta Azure que a porta principal e a chave de recuperação dessa instalação.

* A sua porta principal deve estar a executar a atualização do gateway a partir de novembro de 2017 ou mais tarde.

Depois de configurar o seu gateway principal, quando for instalar outro portal, selecione Adicionar a um cluster de **gateway existente,** selecione o gateway principal, que é o primeiro portal que instalou, e forneça a chave de recuperação para esse gateway. Para obter mais informações, consulte [clusters](https://docs.microsoft.com/data-integration/gateway/service-gateway-install#add-another-gateway-to-create-a-cluster)de alta disponibilidade para gateway de dados no local .

<a name="update-gateway-installation"></a>

## <a name="change-location-migrate-restore-or-take-over-existing-gateway"></a>Alterar a localização, migrar, restaurar ou assumir o gateway existente

Se tiver de alterar a localização do seu portal, desloque a instalação do gateway para um novo computador, recupere um gateway danificado ou tome posse de um gateway existente, precisa da chave de recuperação fornecida durante a instalação do gateway.

1. Executar o instalador de porta de entrada no computador que tenha o gateway existente. Se não tiver o mais recente instalador de gateways, [descarregue a versão mais recente do gateway](https://aka.ms/on-premises-data-gateway-installer).

   > [!NOTE]
   > Antes de restaurar o portal no computador que tem a instalação original do gateway, tem primeiro de desinstalar o portal desse computador. Esta ação desliga o portal original.
   > Se remover ou eliminar um cluster de gateway para qualquer serviço na nuvem, não pode restaurar esse cluster.

1. Depois de o instalador abrir, inscreva-se na mesma conta Azure que foi usada para instalar o portal.

1. **Selecione Migrar, restaurar ou adquirir um gateway** > existente**seguinte,** por exemplo:

   ![Selecione "Migrar, restaurar ou adquirir uma porta de entrada existente"](./media/logic-apps-gateway-install/migrate-recover-take-over-gateway.png)

1. Selecione entre os clusters e gateways disponíveis e introduza a chave de recuperação para o gateway selecionado, por exemplo:

   ![Selecione gateway e forneça chave de recuperação](./media/logic-apps-gateway-install/select-existing-gateway.png)

1. Para mudar a região, selecione **Change Region**e selecione a nova região.

1. Quando estiver pronto, **selecione Configure** para que possa terminar a sua tarefa.

## <a name="tenant-level-administration"></a>Administração a nível de inquilino

Para obter visibilidade em todos os gateways de dados no local de um inquilino da AD Azure, os administradores globais nesse inquilino podem inscrever-se no [centro de administração](https://powerplatform.microsoft.com) da Plataforma De Energia como administrador de inquilinos e selecionar a opção **Data Gateways.** Para obter mais informações, consulte [a administração ao nível do arrendatário para obter a porta de dados no local](https://docs.microsoft.com/data-integration/gateway/service-gateway-tenant-level-admin).

<a name="restart-gateway"></a>

## <a name="restart-gateway"></a>Reiniciar o gateway

Por predefinição, a instalação de gateway no seu computador local funciona como uma conta de serviço Windows chamada "Serviço de gateway de dados no local". No entanto, a `NT SERVICE\PBIEgwService` instalação do gateway utiliza o nome para as suas credenciais de conta "Log on As" e tem permissões "Log on as a service".

> [!NOTE]
> A sua conta de serviço Windows difere da conta utilizada para a ligação a fontes de dados no local e da conta Azure que utiliza quando faz o insessão nos serviços na nuvem.

Como qualquer outro serviço Windows, pode iniciar e parar o portal de várias maneiras. Para mais informações, consulte [Reiniciar uma porta de dados no local](https://docs.microsoft.com/data-integration/gateway/service-gateway-restart).

<a name="gateway-cloud-service"></a>

## <a name="how-the-gateway-works"></a>Como funciona o gateway

Os utilizadores da sua organização podem aceder aos dados no local para os quais já têm acesso autorizado. No entanto, antes de estes utilizadores poderem ligar-se à sua fonte de dados no local, é necessário instalar e configurar um portal de dados no local. Normalmente, um administrador é a pessoa que instala e configura um portal. Estas ações podem requerer permissões do Administrador do Servidor ou conhecimentos especiais sobre os seus servidores no local.

O portal ajuda a facilitar uma comunicação mais rápida e segura nos bastidores. Esta comunicação flui entre um utilizador na nuvem, o serviço de nuvem gateway e a sua fonte de dados no local. O serviço de nuvem gateway encripta e armazena as suas credenciais de origem de dados e detalhes de gateway. O serviço também encaminha as consultas e os seus resultados entre o utilizador, o gateway e a sua fonte de dados no local.

O portal funciona com firewalls e utiliza apenas ligações de saída. Todo o tráfego origina-se como tráfego de saída seguro do agente de porta. O gateway transmite dados de fontes no local em canais encriptados através do [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md). Este ônibus de serviço cria um canal entre o gateway e o serviço de chamada, mas não armazena nenhum dado. Todos os dados que viajam através do portal estão encriptados.

![Arquitetura para gateway de dados no local](./media/logic-apps-gateway-install/how-on-premises-data-gateway-works-flow-diagram.png)

> [!NOTE]
> Dependendo do serviço de nuvem, poderá ser necessário configurar uma fonte de dados para o portal.

Estes passos descrevem o que acontece quando interage com um elemento que está ligado a uma fonte de dados no local:

1. O serviço de nuvem cria uma consulta, juntamente com as credenciais encriptadas para a fonte de dados. O serviço envia então a consulta e as credenciais para a fila de porta de entrada para processamento.

1. O serviço de nuvem gateway analisa a consulta e empurra o pedido para o Azure Service Bus.

1. A Azure Service Bus envia os pedidos pendentes para o portal.

1. O portal obtém a consulta, desencripta as credenciais e conecta-se a uma ou mais fontes de dados com essas credenciais.

1. O portal envia a consulta para a fonte de dados para executar.

1. Os resultados são enviados da fonte de dados de volta para o portal, e depois para o serviço de nuvem gateway. O serviço de nuvem gateway usa então os resultados.

### <a name="authentication-to-on-premises-data-sources"></a>Autenticação em origens de dados no local

Uma credencial armazenada é utilizada para ligar desde a porta de entrada até fontes de dados no local. Independentemente do utilizador, o gateway utiliza a credencial armazenada para se ligar. Pode haver exceções de autenticação para serviços específicos, tais como DirectQuery e LiveConnect para Serviços de Análise em Power BI.

### <a name="azure-active-directory-azure-ad"></a>Azure Active Directory (Azure AD)

Os serviços de nuvem da Microsoft utilizam o [Azure AD](../active-directory/fundamentals/active-directory-whatis.md) para autenticar os utilizadores. Um inquilino da AD Azure contém nomes de utilizadores e grupos de segurança. Normalmente, o endereço de e-mail que utiliza para iniciar sessão é o mesmo que o Nome Principal do Utilizador (UPN) para a sua conta.

### <a name="what-is-my-upn"></a>Qual é a minha UPN?

Se não és um administrador de domínio, podes não conhecer a tua UPN. Para encontrar a UPN para `whoami /upn` a sua conta, dirija o comando da sua estação de trabalho. Embora o resultado pareça um endereço de e-mail, o resultado é a UPN para a sua conta de domínio local.

### <a name="synchronize-an-on-premises-active-directory-with-azure-ad"></a>Sincronizar uma conta do Active Directory no local com o Azure AD

A UPN para as suas contas de Diretório Ativo no local e contas Azure AD deve ser a mesma. Por isso, certifique-se de que cada conta de Diretório Ativo no local corresponde à sua conta Azure AD. Os serviços de nuvem só sabem sobre contas dentro do Azure AD. Então, não precisa adicionar uma conta ao seu Diretório Ativo no local. Se a conta não existir em Azure AD, não pode usar essa conta.

Aqui estão as formas de combinar as suas contas de Diretório Ativo no local com a Azure AD.

* Adicione contas manualmente à AD Azure.

  Crie uma conta no portal Azure ou no centro de administração da Microsoft 365. Certifique-se de que o nome da conta corresponde à UPN para a conta de Diretório Ativo no local.

* Sincronizar as contas locais com o seu inquilino Azure AD utilizando a ferramenta Azure Ative Directory Connect.

  A ferramenta Azure AD Connect oferece opções para a sincronização do diretório e configuração de autenticação. Estas opções incluem sincronização de hash de senha, autenticação pass-through e federação. Se não for um administrador de inquilinoou um administrador de domínio local, contacte o seu administrador de TI para configurar o Azure AD Connect. O Azure AD Connect garante que o seu Azure AD UPN corresponde ao seu Diretório Ativo local UPN. Esta correspondência ajuda se estiver a utilizar ligações ao vivo dos Serviços de Análise com capacidades power BI ou de inscrição única (SSO).

  > [!NOTE]
  > Contas sincronizadas com a ferramenta Azure AD Connect criam novas contas no seu inquilino Azure AD.

<a name="faq"></a>

## <a name="faq-and-troubleshooting"></a>FAQ e resolução de problemas

Para obter mais informações, veja estes tópicos:

* [FAQ sobre o gateway de dados no local](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem-faq)
* [Resolução de problemas na porta de dados no local](https://docs.microsoft.com/data-integration/gateway/service-gateway-tshoot)
* [Monitorizar e otimizar o desempenho de gateways](https://docs.microsoft.com/data-integration/gateway/service-gateway-performance)

## <a name="next-steps"></a>Passos seguintes

* [Ligar-se a dados no local a partir de aplicações lógicas](../logic-apps/logic-apps-gateway-connection.md)
* [Características de integração empresarial](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Conectores para Azure Logic Apps](../connectors/apis-list.md)
