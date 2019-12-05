---
title: 'Tutorial: configurar o WORKDAY para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o workday.
services: active-directory
author: cmmdesai
documentationcenter: na
manager: daveba
ms.assetid: 1a2c375a-1bb1-4a61-8115-5a69972c6ad6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/16/2019
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: 80d356426fe312708d64cc4284dbb1fd925e47c7
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74233335"
---
# <a name="tutorial-configure-workday-for-automatic-user-provisioning"></a>Tutorial: configurar o WORKDAY para provisionamento automático de usuário

O objetivo deste tutorial é mostrar as etapas que você precisa executar para importar perfis de trabalho do WORKDAY para Active Directory e Azure Active Directory, com write-back opcional de endereço de email e nome de usuário para workday.

## <a name="overview"></a>Descrição geral

O [serviço de provisionamento de usuário Azure Active Directory](../manage-apps/user-provisioning.md) integra-se com a [API de recursos humanos do workday](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) para provisionar contas de usuário. O Azure AD usa essa conexão para habilitar os seguintes fluxos de trabalho de provisionamento de usuário:

* **Provisionamento de usuários para Active Directory** -provisionar conjuntos de usuários selecionados do workday em um ou mais domínios de Active Directory.

* O **provisionamento de usuários somente em nuvem para Azure Active Directory** cenários em que o Active Directory local não é usado, os usuários podem ser provisionados diretamente do Workday para Azure Active Directory usando o serviço de provisionamento de usuários do Azure AD.

* **Write-back de endereço de email e nome de usuário para workday** -o serviço de provisionamento de usuário do Azure AD pode gravar os endereços de email e o nome de usuário do Azure AD de volta para o workday.

### <a name="what-human-resources-scenarios-does-it-cover"></a>Quais cenários de recursos humanos ele cobre?

Os fluxos de trabalho de provisionamento de usuário do workday com suporte do serviço de provisionamento de usuário do Azure AD habilitam a automação dos seguintes cenários de gerenciamento de ciclo de vida de identidade e recursos humanos:

* **Contratando novos funcionários** – quando um novo funcionário é adicionado ao workday, uma conta de usuário é criada automaticamente em Active Directory, Azure Active Directory e, opcionalmente, o Office 365 e [outros aplicativos SaaS com suporte do Azure ad](../manage-apps/user-provisioning.md), com write-back do endereço de email para o workday.

* **Atualizações de perfil e atributo de funcionário** – quando um registro de funcionário é atualizado no workday (como seu nome, título ou gerente), sua conta de usuário será atualizada automaticamente em Active Directory, Azure Active Directory e, opcionalmente, no Office 365 e em [outros aplicativos SaaS com suporte do Azure ad](../manage-apps/user-provisioning.md).

* **Encerramentos de funcionários** – quando um funcionário é encerrado no workday, sua conta de usuário é automaticamente desabilitada em Active Directory, Azure Active Directory e, opcionalmente, o Office 365 e [outros aplicativos SaaS com suporte do Azure ad](../manage-apps/user-provisioning.md).

* **Recontratação de funcionário** -quando um funcionário é recontratado no workday, sua conta antiga pode ser reativada automaticamente ou reprovisionada (dependendo da sua preferência) para Active Directory, Azure Active Directory e, opcionalmente, o Office 365 e [outros aplicativos SaaS com suporte do Azure ad](../manage-apps/user-provisioning.md).

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>A qual esta solução de provisionamento de usuário mais adequada?

Essa solução de provisionamento de usuário do workday é ideal para:

* Organizações que desejam uma solução predefinida baseada em nuvem para provisionamento de usuário do workday

* Organizações que exigem o provisionamento direto de usuários do WORKDAY para Active Directory ou Azure Active Directory

* Organizações que exigem que os usuários sejam provisionados usando dados obtidos do módulo de HCM da workday (consulte [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html))

* As organizações que exigem ingressar, mover e deixar os usuários serem sincronizados com uma ou mais florestas Active Directory, domínios e UOs com base apenas nas informações de alteração detectadas no módulo HCM da workday (consulte [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html))

* Organizações que usam o Office 365 para email

## <a name="solution-architecture"></a>Arquitetura da Solução

Esta seção descreve a arquitetura da solução de provisionamento de usuário de ponta a ponta para ambientes híbridos comuns. Há dois fluxos relacionados:

* **Fluxo de dados de RH autoritativo – do WORKDAY para o local Active Directory:** Nesse fluxo, eventos de trabalho (como novas contratações, transferências, terminações) ocorrem pela primeira vez no locatário de RH do workday de nuvem e, em seguida, os dados de evento fluem para Active Directory locais por meio do Azure AD e do agente de provisionamento. Dependendo do evento, ele pode levar a criar/atualizar/habilitar/desabilitar operações no AD.
* **Fluxo de write-back de email e nome de usuário – do local Active Directory para o workday:** Depois que a criação da conta for concluída em Active Directory, ela será sincronizada com o Azure AD por meio do Azure AD Connect, e o atributo de nome de usuário e de email poderá ser gravado novamente no workday.

![Descrição geral](./media/workday-inbound-tutorial/wd_overview.png)

### <a name="end-to-end-user-data-flow"></a>Fluxo de dados do usuário de ponta a ponta

1. A equipe de RH executa transações de trabalho (emissores/movimentadores/pertraçãos ou novas contratações/transferências/terminações) no workday HCM
2. O serviço de provisionamento do Azure AD executa sincronizações agendadas de identidades do workday HR e identifica as alterações que precisam ser processadas para sincronização com Active Directory locais.
3. O serviço de provisionamento do Azure AD invoca o agente de provisionamento local Azure AD Connect com uma carga de solicitação que contém as operações criar/atualizar/habilitar/desabilitar conta do AD.
4. O agente de provisionamento do Azure AD Connect usa uma conta de serviço para adicionar/atualizar dados da conta do AD.
5. O mecanismo de Azure AD Connect/AD Sync executa a sincronização Delta para efetuar pull de atualizações no AD.
6. As atualizações de Active Directory são sincronizadas com Azure Active Directory.
7. Se o conector de write-back do workday estiver configurado, ele gravará o atributo de email e o nome de usuário em workday, com base no atributo correspondente usado.

## <a name="planning-your-deployment"></a>Planejando a implantação

Antes de começar sua integração do workday, verifique os pré-requisitos abaixo e leia as orientações a seguir sobre como corresponder a sua arquitetura de Active Directory atual e os requisitos de provisionamento de usuário com as soluções fornecidas pelo Azure Active Directory. Um [plano de implantação](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-plans) abrangente com planilhas de planejamento também está disponível para ajudá-lo a colaborar com o parceiro de integração do workday e os participantes de RH.

Esta seção aborda os seguintes aspectos do planejamento:

* [Pré-requisitos](#prerequisites)
* [Selecionando os aplicativos do conector de provisionamento a serem implantados](#selecting-provisioning-connector-apps-to-deploy)
* [Planejando a implantação do agente de provisionamento do Azure AD Connect](#planning-deployment-of-azure-ad-connect-provisioning-agent)
* [Integração com vários domínios de Active Directory](#integrating-with-multiple-active-directory-domains)
* [Planejando o WORKDAY para Active Directory mapeamento e transformações de atributos de usuário](#planning-workday-to-active-directory-user-attribute-mapping-and-transformations)

### <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Uma licença de assinatura Azure AD Premium P1 ou superior válida para cada usuário que será originada do workday e provisionada no Active Directory local ou Azure Active Directory.
* Acesso de administrador global do Azure AD para configurar o agente de provisionamento
* Um locatário de implementação do WORKDAY para fins de teste e integração
* Permissões de administrador no WORKDAY para criar um usuário de integração do sistema e fazer alterações para testar dados de funcionários para fins de teste
* Para o provisionamento de usuário para Active Directory, um servidor executando o Windows Server 2012 ou superior com o .NET 4.7.1 + Runtime é necessário para hospedar o [agente de provisionamento local](https://go.microsoft.com/fwlink/?linkid=847801)
* [Azure ad Connect](../hybrid/whatis-hybrid-identity.md) para sincronizar usuários entre o Active Directory e o Azure AD

### <a name="selecting-provisioning-connector-apps-to-deploy"></a>Selecionando os aplicativos do conector de provisionamento a serem implantados

Para facilitar o provisionamento de fluxos de trabalho entre o workday e o Active Directory, o Azure AD fornece vários aplicativos de conector de provisionamento que você pode adicionar da Galeria de aplicativos do Azure AD:

![Galeria de Aplicativo Azure AD](./media/workday-inbound-tutorial/wd_gallery.png)

* **WORKDAY para Active Directory provisionamento de usuário** – esse aplicativo facilita o provisionamento de conta de usuário do WORKDAY para um único domínio de Active Directory. Se você tiver vários domínios, poderá adicionar uma instância desse aplicativo da Galeria de aplicativos do Azure AD para cada domínio de Active Directory ao qual você precisa provisionar.

* **WORKDAY para provisionamento de usuário do Azure ad** -embora Azure ad Connect seja a ferramenta que deve ser usada para sincronizar Active Directory usuários para Azure Active Directory, esse aplicativo pode ser usado para facilitar o provisionamento de usuários somente de nuvem do WORKDAY para um único locatário de Azure Active Directory.

* **Write-back do workday** -este aplicativo facilita a gravação de endereços de email do usuário de Azure Active Directory para o workday.

> [!TIP]
> O aplicativo "workday" regular é usado para configurar o logon único entre o workday e o Azure Active Directory.

Use o gráfico de fluxo de decisão abaixo para identificar quais aplicativos de provisionamento do workday são relevantes para seu cenário.
    ![Fluxograma de decisão](./media/workday-inbound-tutorial/wday_app_flowchart.png "DecisFluxograma de íon ")

Use o Sumário para ir para a seção relevante deste tutorial.

### <a name="planning-deployment-of-azure-ad-connect-provisioning-agent"></a>Planejando a implantação do agente de provisionamento do Azure AD Connect

> [!NOTE]
> Esta seção é relevante apenas se você planeja implantar o WORKDAY para Active Directory aplicativo de provisionamento de usuário. Se você estiver implantando o aplicativo de provisionamento de usuários do Azure AD, você poderá ignorar isso.

O WORKDAY para a solução de provisionamento de usuário do AD requer a implantação de um ou mais agentes de provisionamento em servidores que executam o Windows 2012 R2 ou superior com o mínimo de 4 GB de RAM e do tempo de execução do .NET 4.7.1 +. As considerações a seguir devem ser levadas em conta antes da instalação do agente de provisionamento:

* Verifique se o servidor host que executa o agente de provisionamento tem acesso à rede para o domínio do AD de destino
* O assistente de configuração do agente de provisionamento registra o agente com seu locatário do Azure AD e o processo de registro requer acesso a *. msappproxy.net pela porta SSL 443. Verifique se as regras de firewall de saída estão em vigor para habilitar essa comunicação. O agente dá suporte à [configuração de proxy HTTPS de saída](#how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication).
* O agente de provisionamento usa uma conta de serviço para se comunicar com os domínios do AD local. Antes da instalação do agente, é recomendável que você crie uma conta de serviço com permissões de administrador de domínio e uma senha que não expire.  
* Durante a configuração do agente de provisionamento, você pode selecionar controladores de domínio que devem lidar com solicitações de provisionamento. Se você tiver vários controladores de domínio geograficamente distribuídos, instale o agente de provisionamento no mesmo site que seu (s) controlador (es) de domínio preferido para melhorar a confiabilidade e o desempenho da solução de ponta a ponta
* Para alta disponibilidade, você pode implantar mais de um agente de provisionamento e registrá-lo para lidar com o mesmo conjunto de domínios do AD local.

> [!IMPORTANT]
> Em ambientes de produção, a Microsoft recomenda que você tenha um mínimo de 3 agentes de provisionamento configurados com seu locatário do Azure AD para alta disponibilidade.

### <a name="integrating-with-multiple-active-directory-domains"></a>Integração com vários domínios de Active Directory

> [!NOTE]
> Esta seção é relevante apenas se você planeja implantar o WORKDAY para Active Directory aplicativo de provisionamento de usuário. Se você estiver implantando o aplicativo de provisionamento de usuários do Azure AD, você poderá ignorar isso.

Dependendo de sua topologia de Active Directory, você precisará decidir o número de aplicativos do conector de provisionamento do usuário e o número de agentes de provisionamento a serem configurados. Abaixo estão listados alguns dos padrões comuns de implantação que você pode consultar ao planejar sua implantação.

#### <a name="deployment-scenario-1--single-workday-tenant---single-ad-domain"></a>Cenário de implantação #1: locatário do workday único-> domínio único do AD

Nesse cenário, você tem um locatário do workday e deseja provisionar usuários para um único domínio do AD de destino. Aqui está a configuração de produção recomendada para essa implantação.

|   |   |
| - | - |
| Não. de provisionamento de agentes para implantação local | 3 (para alta disponibilidade e failover) |
| Não. do WORKDAY para os aplicativos de provisionamento de usuário do AD a serem configurados no portal do Azure | 1 |

  ![Cenário 1](./media/workday-inbound-tutorial/dep_scenario1.png)

#### <a name="deployment-scenario-2--single-workday-tenant---multiple-child-ad-domains"></a>Cenário de implantação #2: locatário do workday único-> vários domínios filho do AD

Esse cenário envolve o provisionamento de usuários do WORKDAY para vários domínios filho do AD de destino em uma floresta. Aqui está a configuração de produção recomendada para essa implantação.

|   |   |
| - | - |
| Não. de provisionamento de agentes para implantação local | 3 (para alta disponibilidade e failover) |
| Não. do WORKDAY para os aplicativos de provisionamento de usuário do AD a serem configurados no portal do Azure | um aplicativo por domínio filho |

  ![Cenário 2](./media/workday-inbound-tutorial/dep_scenario2.png)

#### <a name="deployment-scenario-3--single-workday-tenant---disjoint-ad-forests"></a>Cenário de implantação #3: único locatário do workday-> florestas do AD não contíguos

Esse cenário envolve o provisionamento de usuários do WORKDAY para domínios em florestas do AD não contíguos. Aqui está a configuração de produção recomendada para essa implantação.

|   |   |
| - | - |
| Não. de provisionamento de agentes para implantação local | 3 por floresta de AD não contíguo |
| Não. do WORKDAY para os aplicativos de provisionamento de usuário do AD a serem configurados no portal do Azure | um aplicativo por domínio filho |

  ![Cenário 3](./media/workday-inbound-tutorial/dep_scenario3.png)

### <a name="planning-workday-to-active-directory-user-attribute-mapping-and-transformations"></a>Planejando o WORKDAY para Active Directory mapeamento e transformações de atributos de usuário

> [!NOTE]
> Esta seção é relevante apenas se você planeja implantar o WORKDAY para Active Directory aplicativo de provisionamento de usuário. Se você estiver implantando o aplicativo de provisionamento de usuários do Azure AD, você poderá ignorar isso.

Antes de configurar o provisionamento de usuário para um domínio Active Directory, considere as perguntas a seguir. As respostas a essas perguntas determinarão como os filtros de escopo e os mapeamentos de atributo precisam ser definidos.

* **Quais usuários do workday precisam ser provisionados para esta Active Directory floresta?**

  * *Exemplo: usuários em que o atributo "Company" do workday contém o valor "contoso" e o atributo "Worker_Type" contém "regular"*

* **Como os usuários são roteados em UOs (unidades organizacionais) diferentes?**

  * *Exemplo: os usuários são roteados para UOs que correspondem a um local do escritório, conforme definido nos atributos "municipal" e "Country_Region_Reference" do workday*

* **Como os seguintes atributos devem ser populados no Active Directory?**

  * Nome comum (CN)
    * *Exemplo: usar o valor de User_ID do workday, conforme definido por recursos humanos*

  * ID do funcionário (employeeId)
    * *Exemplo: usar o valor de Worker_ID do workday*

  * Nome da conta SAM (sAMAccountName)
    * *Exemplo: usar o valor de User_ID do workday, filtrado por meio de uma expressão de provisionamento do Azure AD para remover caracteres ilegais*

  * Nome principal do usuário (userPrincipalName)
    * *Exemplo: usar o valor de User_ID do workday, com uma expressão de provisionamento do Azure AD para acrescentar um nome de domínio*

* **Como os usuários devem ser correspondidos entre o workday e o Active Directory?**

  * *Exemplo: os usuários com um valor "Worker_ID" de workday específico são combinados com Active Directory usuários em que "employeeID" tem o mesmo valor. Se o valor de Worker_ID não for encontrado em Active Directory, crie um novo usuário.*
  
* **O Active Directory floresta já contém as IDs de usuário necessárias para que a lógica de correspondência funcione?**

  * *Exemplo: se essa configuração for uma nova implantação do workday, é recomendável que Active Directory seja preenchida previamente com os valores de Worker_ID do workday corretos (ou o valor de ID exclusivo de sua escolha) para manter a lógica de correspondência o mais simples possível.*

Como configurar e configurar esses aplicativos de conector de provisionamento especial é o assunto das seções restantes deste tutorial. Os aplicativos que você escolher configurar dependerão de quais sistemas você precisa provisionar e quantos domínios Active Directory e locatários do AD do Azure estão em seu ambiente.

## <a name="configure-integration-system-user-in-workday"></a>Configurar o usuário do sistema de integração no workday

Um requisito comum de todos os conectores de provisionamento do workday é que eles exigem credenciais de um usuário do sistema de integração do WORKDAY para se conectar à API de recursos humanos do workday. Esta seção descreve como criar um usuário do sistema de integração no workday e tem as seguintes seções:

* [Criando um usuário do sistema de integração](#creating-an-integration-system-user)
* [Criando um grupo de segurança de integração](#creating-an-integration-security-group)
* [Configurando permissões de política de segurança de domínio](#configuring-domain-security-policy-permissions)
* [Configurando permissões de política de segurança do processo comercial](#configuring-business-process-security-policy-permissions)
* [Ativando alterações de política de segurança](#activating-security-policy-changes)

> [!NOTE]
> É possível ignorar esse procedimento e, em vez disso, usar uma conta de administrador global do workday como a conta de integração do sistema. Isso pode funcionar bem para demonstrações, mas não é recomendado para implantações de produção.

### <a name="creating-an-integration-system-user"></a>Criando um usuário do sistema de integração

**Para criar um usuário do sistema de integração:**

1. Entre em seu locatário do workday usando uma conta de administrador. No **aplicativo workday**, digite criar usuário na caixa de pesquisa e, em seguida, clique em **criar usuário do sistema de integração**.

    ![Criar usuário](./media/workday-inbound-tutorial/wd_isu_01.png "Criar utilizador")
2. Conclua a tarefa **criar usuário do sistema de integração** fornecendo um nome de usuário e senha para um novo usuário do sistema de integração.  
  
* Deixe a opção **exigir nova senha na próxima entrada** desmarcada, pois esse usuário fará logon por meio de programação.
* Deixe o **tempo limite da sessão em minutos** com seu valor padrão de 0, o que impedirá que as sessões do usuário atinjam o tempo de ociosidade prematuramente.
* Selecione a opção **não permitir sessões de interface do usuário** , pois ela fornece uma camada de segurança adicional que impede que um usuário com a senha do sistema de integração faça logon no workday.

    ![Criar usuário do sistema de integração](./media/workday-inbound-tutorial/wd_isu_02.png "Criar usuário do sistema de integração")

### <a name="creating-an-integration-security-group"></a>Criando um grupo de segurança de integração

Nesta etapa, você criará um grupo de segurança de sistema de integração irrestrito ou restrito no workday e atribuirá o usuário do sistema de integração criado na etapa anterior a esse grupo.

**Para criar um grupo de segurança:**

1. Digite criar grupo de segurança na caixa de pesquisa e, em seguida, clique em **Criar grupo de segurança**.

    ![Grupo createsecurity](./media/workday-inbound-tutorial/wd_isu_03.png "Grupo createsecurity")
2. Conclua a tarefa **Criar grupo de segurança** . 

   * Há dois tipos de grupos de segurança no workday:
     * **Irrestrito:** Todos os membros do grupo de segurança podem acessar todas as instâncias de dados protegidas pelo grupo de segurança.
     * **Restrito:** Todos os membros do grupo de segurança têm acesso contextual a um subconjunto de instâncias de dados (linhas) que o grupo de segurança pode acessar.
   * Consulte seu parceiro de integração do WORKDAY para selecionar o tipo de grupo de segurança apropriado para a integração.
   * Depois de saber o tipo de grupo, selecione **grupo de segurança do sistema de integração (Irrestrito)** ou **grupo de segurança do sistema de integração (restrito)** no menu suspenso **tipo de grupo de segurança de locatários** .

     ![Grupo createsecurity](./media/workday-inbound-tutorial/wd_isu_04.png "Grupo createsecurity")

3. Depois que a criação do grupo de segurança for bem-sucedida, você verá uma página em que você pode atribuir Membros ao grupo de segurança. Adicione o novo usuário do sistema de integração criado na etapa anterior a esse grupo de segurança. Se você estiver usando um grupo de segurança *restrito* , também precisará selecionar o escopo apropriado da organização.

    ![Editar grupo de segurança](./media/workday-inbound-tutorial/wd_isu_05.png "Editar grupo de segurança")

### <a name="configuring-domain-security-policy-permissions"></a>Configurando permissões de política de segurança de domínio

Nesta etapa, você concederá permissões de política de "segurança de domínio" para os dados de trabalho para o grupo de segurança.

**Para configurar permissões de política de segurança de domínio:**

1. Insira **configuração de segurança de domínio** na caixa de pesquisa e, em seguida, clique no link **configuração de segurança de domínio**.  

    ![Políticas de segurança de domínio](./media/workday-inbound-tutorial/wd_isu_06.png "Políticas de segurança de domínio")  
2. Na caixa de texto **domínio** , procure os domínios a seguir e adicione-os ao filtro um por um.  
   * *Provisionamento de conta externa*
   * *Dados de trabalho: relatórios de trabalho públicos*
   * *Dados da pessoa: informações de contato de trabalho*
   * *Dados de trabalho: todas as posições*
   * *Dados de trabalho: informações sobre a equipe atual*
   * *Dados de trabalho: título comercial no perfil de trabalho*
   * *Contas de workday*
   
     ![Políticas de segurança de domínio](./media/workday-inbound-tutorial/wd_isu_07.png "Políticas de segurança de domínio")  

     ![Políticas de segurança de domínio](./media/workday-inbound-tutorial/wd_isu_08.png "Políticas de segurança de domínio") 

     Clique em **OK**.

3. No relatório exibido, selecione as reticências (...) que aparece ao lado de **provisionamento de conta externa** e clique na opção de menu **domínio-> Editar permissões de política de segurança**

    ![Políticas de segurança de domínio](./media/workday-inbound-tutorial/wd_isu_09.png "Políticas de segurança de domínio")  

4. Na página **Editar permissões de política de segurança de domínio** , role para baixo até a seção **permissões de integração**. Clique no sinal de "+" para adicionar o grupo do sistema de integração à lista de grupos de segurança com permissões de integração **Get** e **Put** .

    ![Editar permissão](./media/workday-inbound-tutorial/wd_isu_10.png "Editar permissão")  

5. Clique no sinal de "+" para adicionar o grupo do sistema de integração à lista de grupos de segurança com permissões de integração **Get** e **Put** .

    ![Editar permissão](./media/workday-inbound-tutorial/wd_isu_11.png "Editar permissão")  

6. Repita as etapas 3-5 acima para cada uma dessas políticas de segurança restantes:

   | Operação | Política de segurança de domínio |
   | ---------- | ---------- |
   | Obter e colocar | Dados de trabalho: relatórios de trabalho públicos |
   | Obter e colocar | Dados da pessoa: informações de contato de trabalho |
   | Get | Dados de trabalho: todas as posições |
   | Get | Dados de trabalho: informações sobre a equipe atual |
   | Get | Dados de trabalho: título comercial no perfil de trabalho |
   | Obter e colocar | Contas de workday |

### <a name="configuring-business-process-security-policy-permissions"></a>Configurando permissões de política de segurança do processo comercial

Nesta etapa, você concederá permissões de política de "segurança de processo empresarial" para os dados de trabalho para o grupo de segurança. Esta etapa é necessária para configurar o conector do aplicativo de write-back do workday.

**Para configurar permissões de política de segurança do processo de negócios:**

1. Insira **política de processo de negócios** na caixa de pesquisa e, em seguida, clique na tarefa **Editar política de segurança do processo comercial** .  

    ![Políticas de segurança do processo comercial](./media/workday-inbound-tutorial/wd_isu_12.png "Políticas de segurança do processo comercial")  

2. Na caixa de texto **tipo de processo comercial** , *procure contato* e selecione **contato alterar** processo comercial e clique em **OK**.

    ![Políticas de segurança do processo comercial](./media/workday-inbound-tutorial/wd_isu_13.png "Políticas de segurança do processo comercial")  

3. Na página **Editar política de segurança do processo comercial** , role até a seção **manter informações de contato (serviço Web)** .

    ![Políticas de segurança do processo comercial](./media/workday-inbound-tutorial/wd_isu_14.png "Políticas de segurança do processo comercial")  

4. Selecione e adicione o novo grupo de segurança do sistema de integração à lista de grupos de segurança que podem iniciar a solicitação de serviços Web. Clique em **concluído**. 

    ![Políticas de segurança do processo comercial](./media/workday-inbound-tutorial/wd_isu_15.png "Políticas de segurança do processo comercial")  

### <a name="activating-security-policy-changes"></a>Ativando alterações de política de segurança

**Para ativar as alterações de política de segurança:**

1. Digite ativar na caixa de pesquisa e, em seguida, clique no link **Ativar alterações de política de segurança pendentes**.

    ![Ativar](./media/workday-inbound-tutorial/wd_isu_16.png "Ativar")

1. Inicie a tarefa ativar alterações de política de segurança pendentes inserindo um comentário para fins de auditoria e clique em **OK**.
1. Conclua a tarefa na próxima tela marcando a caixa de seleção **confirmar**e clique em **OK**.

    ![Ativar segurança pendente](./media/workday-inbound-tutorial/wd_isu_18.png "Ativar segurança pendente")  

## <a name="configuring-user-provisioning-from-workday-to-active-directory"></a>Configurando o provisionamento de usuário do WORKDAY para o Active Directory

Esta seção fornece etapas para o provisionamento de contas de usuário do WORKDAY para cada domínio Active Directory dentro do escopo da sua integração.

* [Instalar e configurar agente (s) de provisionamento local](#part-1-install-and-configure-on-premises-provisioning-agents)
* [Adicionando o aplicativo do conector de provisionamento e criando a conexão com o workday](#part-2-adding-the-provisioning-connector-app-and-creating-the-connection-to-workday)
* [Configurar mapeamentos de atributo](#part-3-configure-attribute-mappings)
* [Habilitar e iniciar o provisionamento de usuário](#enable-and-launch-user-provisioning)

### <a name="part-1-install-and-configure-on-premises-provisioning-agents"></a>Parte 1: instalar e configurar agente (s) de provisionamento local

Para provisionar para Active Directory local, um agente deve ser instalado em um servidor que tenha o .NET 4.7.1 + estrutura e acesso de rede aos domínios de Active Directory desejados.

> [!TIP]
> Você pode verificar a versão do .NET Framework no seu servidor usando as instruções fornecidas [aqui](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed).
> Se o servidor não tiver o .NET 4.7.1 ou superior instalado, você poderá baixá-lo [aqui](https://support.microsoft.com/help/4033342/the-net-framework-4-7-1-offline-installer-for-windows).  

Depois de implantar o .NET 4.7.1 +, você pode baixar o **[agente de provisionamento local aqui](https://go.microsoft.com/fwlink/?linkid=847801)** e seguir as etapas fornecidas abaixo para concluir a configuração do agente.

1. Entre no Windows Server em que você deseja instalar o novo agente.

1. Inicie o instalador do agente de provisionamento, concorde com os termos e clique no botão **instalar** .

   ![Tela de instalação](./media/workday-inbound-tutorial/pa_install_screen_1.png "Tela de instalação")
   
1. Após a conclusão da instalação, o assistente será iniciado e você verá a tela **conectar o Azure ad** . Clique no botão **autenticar** para se conectar à sua instância do Azure AD.

   ![Conectar o Azure AD](./media/workday-inbound-tutorial/pa_install_screen_2.png "Ligar ao Azure AD")
   
1. Autentique em sua instância do Azure AD usando credenciais de administrador global.

   ![Autenticação de administrador](./media/workday-inbound-tutorial/pa_install_screen_3.png "Autenticação de administrador")

   > [!NOTE]
   > As credenciais de administrador do Azure AD são usadas somente para se conectar ao seu locatário do Azure AD. O agente não armazena as credenciais localmente no servidor.

1. Após a autenticação bem-sucedida com o Azure AD, você verá a tela **conectar Active Directory** . Nesta etapa, insira o nome de domínio do AD e clique no botão **Adicionar diretório** .

   ![Adicionar diretório](./media/workday-inbound-tutorial/pa_install_screen_4.png "Adicionar diretório")
  
1. Agora, você será solicitado a inserir as credenciais necessárias para se conectar ao domínio do AD. Na mesma tela, você pode usar a **prioridade selecionar controlador de domínio** para especificar os controladores de domínio que o agente deve usar para enviar solicitações de provisionamento.

   ![Credenciais de domínio](./media/workday-inbound-tutorial/pa_install_screen_5.png)
   
1. Depois de configurar o domínio, o instalador exibirá uma lista de domínios configurados. Nessa tela, você pode repetir a etapa #5 e #6 para adicionar mais domínios ou clicar em **Avançar** para prosseguir para o registro do agente.

   ![Domínios configurados](./media/workday-inbound-tutorial/pa_install_screen_6.png "Domínios configurados")

   > [!NOTE]
   > Se você tiver vários domínios do AD (por exemplo, na.contoso.com, emea.contoso.com), adicione cada domínio individualmente à lista.
   > Somente a adição do domínio pai (por exemplo, contoso.com) não é suficiente. Você deve registrar cada domínio filho com o agente.
   
1. Examine os detalhes de configuração e clique em **confirmar** para registrar o agente.
  
   ![Confirmar tela](./media/workday-inbound-tutorial/pa_install_screen_7.png "Confirmar tela")
   
1. O assistente de configuração exibe o progresso do registro do agente.
  
   ![Registro do agente](./media/workday-inbound-tutorial/pa_install_screen_8.png "Registro do agente")
   
1. Quando o registro do agente for bem-sucedido, você poderá clicar em **sair** para sair do assistente.
  
   ![Sair da tela](./media/workday-inbound-tutorial/pa_install_screen_9.png "Sair da tela")
   
1. Verifique a instalação do agente e verifique se ele está em execução abrindo o snap-in "serviços" e procure o serviço chamado "agente de provisionamento do Microsoft Azure AD Connect"
  
   ![Serviços](./media/workday-inbound-tutorial/services.png)

### <a name="part-2-adding-the-provisioning-connector-app-and-creating-the-connection-to-workday"></a>Parte 2: adicionando o aplicativo do conector de provisionamento e criando a conexão com o workday

**Para configurar o WORKDAY para Active Directory provisionamento:**

1. Ir para <https://portal.azure.com>

2. Na barra de navegação à esquerda, selecione **Azure Active Directory**

3. Selecione **aplicativos empresariais**e **todos os aplicativos**.

4. Selecione **Adicionar um aplicativo**e selecione a categoria **tudo** .

5. Pesquise o **provisionamento do WORKDAY para Active Directory**e adicione esse aplicativo da galeria.

6. Depois que o aplicativo for adicionado e a tela de detalhes do aplicativo for exibida, selecione **provisionamento**

7. Alterar o **modo** de provisionamento para **automático**

8. Conclua a seção de **credenciais de administrador** da seguinte maneira:

   * **Nome de usuário do administrador** – Insira o nome de usuário da conta do sistema de integração do workday, com o nome de domínio do locatário anexado. Ele deve ser semelhante a: **username\@tenant_name**

   * **Senha de administrador –** Insira a senha da conta do sistema de integração do workday

   * **URL do locatário –** Insira a URL para o ponto de extremidade de serviços Web do WORKDAY para seu locatário. Esse valor deve ser semelhante a: https://wd3-impl-services1.workday.com/ccx/service/contoso4, em que *contoso4* é substituído pelo nome do locatário correto e *WD3-impl* é substituído pela cadeia de caracteres de ambiente correta.

   * **Floresta Active Directory** O "nome" de seu domínio de Active Directory, conforme registrado com o agente. Use a lista suspensa para selecionar o domínio de destino para provisionamento. Esse valor normalmente é uma cadeia de caracteres como: *contoso.com*

   * **Contêiner de Active Directory-** Insira o DN do contêiner em que o agente deve criar contas de usuário por padrão.
        Exemplo: *ou = usuários padrão, ou = usuários, DC = contoso, DC = teste*
        
     > [!NOTE]
     > Essa configuração só entra em cena para criações de conta de usuário se o atributo *parentDistinguishedName* não estiver configurado nos mapeamentos de atributo. Essa configuração não é usada para operações de pesquisa ou atualização de usuário. A subárvore de domínio inteira cai no escopo da operação de pesquisa.

   * **Email de notificação –** Insira seu endereço de email e marque a caixa de seleção "enviar email se ocorrer falha".

     > [!NOTE]
     > O serviço de provisionamento do Azure AD enviará uma notificação por email se o trabalho de provisionamento entrar em um estado de [quarentena](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning#quarantine) .

   * Clique no botão **testar conexão** . Se o teste de conexão tiver sucesso, clique no botão **salvar** na parte superior. Se ele falhar, verifique se as credenciais do workday e as credenciais do AD configuradas na instalação do agente são válidas.

     ![Portal do Azure](./media/workday-inbound-tutorial/wd_1.png)

   * Depois que as credenciais forem salvas com êxito, a seção **mapeamentos** exibirá o mapeamento padrão **sincronizar trabalhos do WORKDAY para o local Active Directory**

### <a name="part-3-configure-attribute-mappings"></a>Parte 3: configurar mapeamentos de atributo

Nesta seção, você configurará como os dados do usuário fluem do WORKDAY para o Active Directory.

1. Na guia provisionamento em **mapeamentos**, clique em **sincronizar trabalhos do WORKDAY para o local Active Directory**.

1. No campo **escopo do objeto de origem** , você pode selecionar quais conjuntos de usuários no workday devem estar no escopo para provisionamento no AD, definindo um conjunto de filtros baseados em atributo. O escopo padrão é "todos os usuários no workday". Filtros de exemplo:

   * Exemplo: escopo para usuários com IDs de trabalho entre 1 milhão e 2 milhões (exceto 2 milhões)

      * Atributo: Workerid

      * Operador: correspondência de REGEX

      * Valor: (1 [0-9] [0-9] [0-9] [0-9] [0-9] [0-9])

   * Exemplo: somente funcionários e não usuários contingentes

      * Atributo: EmployeeID

      * Operador: não é nulo

   > [!TIP]
   > Quando você estiver configurando o aplicativo de provisionamento pela primeira vez, será necessário testar e verificar os mapeamentos e as expressões de atributo para garantir que ele esteja dando o resultado desejado. A Microsoft recomenda usar os filtros de escopo no **escopo do objeto de origem** para testar seus mapeamentos com alguns usuários de teste do workday. Depois de verificar se os mapeamentos funcionam, você pode remover o filtro ou expandi-lo gradualmente para incluir mais usuários.

   > [!CAUTION] 
   > O comportamento padrão do mecanismo de provisionamento é desabilitar/excluir usuários que saem do escopo. Isso pode não ser desejável em sua integração do seu workday com o AD. Para substituir esse comportamento padrão, consulte o artigo [ignorar a exclusão de contas de usuário que saem do escopo](../manage-apps/skip-out-of-scope-deletions.md)
  
1. No campo **ações do objeto de destino** , você pode filtrar globalmente quais ações são executadas em Active Directory. **Criar** e **Atualizar** são os mais comuns.

1. Na seção **mapeamentos de atributo** , você pode definir como os atributos individuais do workday são mapeados para Active Directory atributos.

1. Clique em um mapeamento de atributo existente para atualizá-lo ou clique em **Adicionar novo mapeamento** na parte inferior da tela para adicionar novos mapeamentos. Um mapeamento de atributo individual oferece suporte a essas propriedades:

      * **Tipo de mapeamento**

         * **Direto** – grava o valor do atributo workday no atributo do AD, sem alterações

         * **Constante** -escreva um valor de cadeia de caracteres constante e estático para o atributo do AD

         * **Expression** – permite que você grave um valor personalizado para o atributo do AD, com base em um ou mais atributos do workday. [Para obter mais informações, consulte este artigo sobre expressões](../manage-apps/functions-for-customizing-application-data.md).

      * **Atributo de origem** -o atributo de usuário do workday. Se o atributo que você está procurando não estiver presente, consulte [Personalizando a lista de atributos de usuário do workday](#customizing-the-list-of-workday-user-attributes).

      * **Valor padrão** – opcional. Se o atributo de origem tiver um valor vazio, o mapeamento gravará esse valor em vez disso.
            A configuração mais comum é deixar em branco.

      * **Atributo de destino** – o atributo de usuário em Active Directory.

      * **Corresponder objetos usando este atributo** – se este mapeamento deve ou não ser usado para identificar exclusivamente os usuários entre o workday e o Active Directory. Esse valor é normalmente definido no campo ID de trabalho para workday, que normalmente é mapeado para um dos atributos de ID de funcionário no Active Directory.

      * **Precedência de correspondência** – vários atributos correspondentes podem ser definidos. Quando há vários, eles são avaliados na ordem definida por esse campo. Assim que uma correspondência for encontrada, nenhum atributo correspondente será avaliado.

      * **Aplicar este mapeamento**

         * **Sempre** – aplicar esse mapeamento nas ações de criação e atualização do usuário

         * **Somente durante a criação** -aplique este mapeamento somente em ações de criação de usuário

1. Para salvar seus mapeamentos, clique em **salvar** na parte superior da seção de mapeamento de atributos.

   ![Portal do Azure](./media/workday-inbound-tutorial/wd_2.png)

#### <a name="below-are-some-example-attribute-mappings-between-workday-and-active-directory-with-some-common-expressions"></a>Abaixo estão alguns exemplos de mapeamentos de atributo entre workday e Active Directory, com algumas expressões comuns

* A expressão que mapeia para o atributo *parentDistinguishedName* é usada para provisionar um usuário para UOs diferentes com base em um ou mais atributos de origem do workday. Este exemplo aqui coloca os usuários em UOs diferentes com base em qual cidade eles estão.

* O atributo *userPrincipalName* em Active Directory é gerado usando a função de eliminação de duplicação [SelectUniqueValue](../manage-apps/functions-for-customizing-application-data.md#selectuniquevalue) que verifica a existência de um valor gerado no domínio do AD de destino e só o define se ele for exclusivo.  

* [Há documentação sobre expressões de escrita aqui](../manage-apps/functions-for-customizing-application-data.md). Esta seção inclui exemplos de como remover caracteres especiais.

| ATRIBUTO WORKDAY | ATRIBUTO DO ACTIVE DIRECTORY |  ID DE CORRESPONDÊNCIA? | CRIAR/ATUALIZAR |
| ---------- | ---------- | ---------- | ---------- |
| **Workerid**  |  Funcionário | **Sim** | Gravado em criar somente |
| **PreferredNameData**    |  CN    |   |   Gravado em criar somente |
| **SelectUniqueValue (Join ("\@", Join (".", \[FirstName\], \[LastName\]), "contoso.com"), junção ("\@", junção (".", mid (\[FirstName\], 1, 1), \[LastName\]), "contoso.com"), junção ("\@", Join (".", mid (\[FirstName\], 1, 2), \[LastName\]), "contoso.com"))**   | userPrincipalName     |     | Gravado em criar somente 
| **Replace (EXT (Replace (\[UserID\],, "(\[\\\\/\\\\\\\\\\\\\[\\\\\]\\\\:\\\\;\\\\\|\\\\=\\\\,\\\\+\\\\\*\\\\?\\\\&lt;\\\\&gt;\]) ",," ",,), 1, 20),," ([\\\\.)\*\$] (file:///\\). *$)", , "", , )**      |    sAMAccountName            |     |         Gravado em criar somente |
| **Switch (\[active\],, "0", "true", "1", "false")** |  accountDisabled      |     | Criar + atualizar |
| **FirstName**   | givenName       |     |    Criar + atualizar |
| **LastName**   |   sn   |     |  Criar + atualizar |
| **PreferredNameData**  |  displayName |     |   Criar + atualizar |
| **Corporativa**         | Empresa   |     |  Criar + atualizar |
| **SupervisoryOrganization**  | Departamento  |     |  Criar + atualizar |
| **ManagerReference**   | Manager  |     |  Criar + atualizar |
| **BusinessTitle**   |  Título     |     |  Criar + atualizar | 
| **AddressLineData**    |  streetAddress  |     |   Criar + atualizar |
| **Município**   |   l   |     | Criar + atualizar |
| **CountryReferenceTwoLetter**      |   Co |     |   Criar + atualizar |
| **CountryReferenceTwoLetter**    |  c  |     |         Criar + atualizar |
| **CountryRegionReference** |  St     |     | Criar + atualizar |
| **WorkSpaceReference** | physicalDeliveryOfficeName    |     |  Criar + atualizar |
| **PostalCode**  |   postalCode  |     | Criar + atualizar |
| **PrimaryWorkTelephone**  |  telephoneNumber   |     | Criar + atualizar |
| <bpt id="p1">**</bpt>Fax<ept id="p1">**</ept>      | facsimileTelephoneNumber     |     |    Criar + atualizar |
| **Móveis**  |    móvel       |     |       Criar + atualizar |
| **LocalReference** |  preferredLanguage  |     |  Criar + atualizar |                                               
| **Switch (\[\]Municipal, "OU = Standard Users, OU = Users, OU = Default, OU = Locations, DC = contoso, DC = com", "Dallas", "OU = Standard Users, OU = Users, OU = Dallas, OU = Locations, DC = contoso, DC = com", "Austin", "OU = Standard Users, OU = Users, OU OU = Locations, DC = contoso, DC = com", "Seattle", "OU = Users Standard, OU = Users, OU = Seattle, OU = Locations, DC = contoso, DC = com", "Londres", "OU = Users Standard, OU = Users, OU = London, OU = Locations = contoso, DC = com"**  | parentDistinguishedName     |     |  Criar + atualizar |

Depois que a configuração de mapeamento de atributo for concluída, agora você poderá [habilitar e iniciar o serviço de provisionamento de usuário](#enable-and-launch-user-provisioning).

## <a name="configuring-user-provisioning-to-azure-ad"></a>Configurando o provisionamento de usuário para o Azure AD

As seções a seguir descrevem as etapas para configurar o provisionamento de usuário do WORKDAY para o Azure AD para implantações somente em nuvem.

* [Adicionando o aplicativo conector de provisionamento do Azure AD e criando a conexão com o workday](#part-1-adding-the-azure-ad-provisioning-connector-app-and-creating-the-connection-to-workday)
* [Configurar o workday e mapeamentos de atributo do Azure AD](#part-2-configure-workday-and-azure-ad-attribute-mappings)
* [Habilitar e iniciar o provisionamento de usuário](#enable-and-launch-user-provisioning)

> [!IMPORTANT]
> Siga o procedimento abaixo se você tiver usuários somente de nuvem que precisam ser provisionados no Azure AD e não no local Active Directory.

### <a name="part-1-adding-the-azure-ad-provisioning-connector-app-and-creating-the-connection-to-workday"></a>Parte 1: adicionando o aplicativo do conector de provisionamento do Azure AD e criando a conexão com o workday

**Para configurar o WORKDAY para Azure Active Directory provisionamento para usuários somente de nuvem:**

1. Aceda a <https://portal.azure.com>.

2. Na barra de navegação à esquerda, selecione **Azure Active Directory**

3. Selecione **aplicativos empresariais**e **todos os aplicativos**.

4. Selecione **Adicionar um aplicativo**e, em seguida, selecione a categoria **tudo** .

5. Pesquise por **workday no provisionamento do Azure ad**e adicione esse aplicativo da galeria.

6. Depois que o aplicativo for adicionado e a tela de detalhes do aplicativo for exibida, selecione **provisionamento**

7. Alterar o **modo** de provisionamento para **automático**

8. Conclua a seção de **credenciais de administrador** da seguinte maneira:

   * **Nome de usuário do administrador** – Insira o nome de usuário da conta do sistema de integração do workday, com o nome de domínio do locatário anexado. Deve ser semelhante a: username@contoso4

   * **Senha de administrador –** Insira a senha da conta do sistema de integração do workday

   * **URL do locatário –** Insira a URL para o ponto de extremidade de serviços Web do WORKDAY para seu locatário. Esse valor deve ser semelhante a: https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources, em que *contoso4* é substituído pelo nome do locatário correto e *WD3-impl* é substituído pela cadeia de caracteres de ambiente correta. Se essa URL não for conhecida, trabalhe com o parceiro de integração do workday ou com o representante de suporte para determinar a URL correta a ser usada.

   * **Email de notificação –** Insira seu endereço de email e marque a caixa de seleção "enviar email se ocorrer falha".

   * Clique no botão **testar conexão** .

   * Se o teste de conexão tiver sucesso, clique no botão **salvar** na parte superior. Se falhar, verifique se a URL e as credenciais do workday são válidas no workday.

### <a name="part-2-configure-workday-and-azure-ad-attribute-mappings"></a>Parte 2: configurar o workday e os mapeamentos de atributo do Azure AD

Nesta seção, você configurará como os dados do usuário fluem do WORKDAY para Azure Active Directory para usuários somente na nuvem.

1. Na guia provisionamento, em **mapeamentos**, clique em **sincronizar trabalhadores com o Azure ad**.

2. No campo **escopo do objeto de origem** , você pode selecionar quais conjuntos de usuários no workday devem estar no escopo para provisionamento para o Azure AD, definindo um conjunto de filtros baseados em atributo. O escopo padrão é "todos os usuários no workday". Filtros de exemplo:

   * Exemplo: escopo para usuários com IDs de trabalho entre 1 milhão e 2 milhões

      * Atributo: Workerid

      * Operador: correspondência de REGEX

      * Valor: (1 [0-9] [0-9] [0-9] [0-9] [0-9] [0-9])

   * Exemplo: somente trabalhadores contingentes e não funcionários regulares

      * Atributo: Contingentid

      * Operador: não é nulo

3. No campo **ações do objeto de destino** , você pode filtrar globalmente quais ações são executadas no Azure AD. **Criar** e **Atualizar** são os mais comuns.

4. Na seção **mapeamentos de atributo** , você pode definir como os atributos individuais do workday são mapeados para Active Directory atributos.

5. Clique em um mapeamento de atributo existente para atualizá-lo ou clique em **Adicionar novo mapeamento** na parte inferior da tela para adicionar novos mapeamentos. Um mapeamento de atributo individual oferece suporte a essas propriedades:

   * **Tipo de mapeamento**

      * **Direto** – grava o valor do atributo workday no atributo do AD, sem alterações

      * **Constante** -escreva um valor de cadeia de caracteres constante e estático para o atributo do AD

      * **Expression** – permite que você grave um valor personalizado para o atributo do AD, com base em um ou mais atributos do workday. [Para obter mais informações, consulte este artigo sobre expressões](../manage-apps/functions-for-customizing-application-data.md).

   * **Atributo de origem** -o atributo de usuário do workday. Se o atributo que você está procurando não estiver presente, consulte [Personalizando a lista de atributos de usuário do workday](#customizing-the-list-of-workday-user-attributes).

   * **Valor padrão** – opcional. Se o atributo de origem tiver um valor vazio, o mapeamento gravará esse valor em vez disso.
            A configuração mais comum é deixar em branco.

   * **Atributo de destino** – o atributo de usuário no Azure AD.

   * **Corresponder objetos usando este atributo** – se este atributo deve ou não ser usado para identificar exclusivamente os usuários entre o workday e o Azure AD. Esse valor é normalmente definido no campo ID de trabalho para workday, que é normalmente mapeado para o atributo de ID de funcionário (novo) ou um atributo de extensão no Azure AD.

   * **Precedência de correspondência** – vários atributos correspondentes podem ser definidos. Quando há vários, eles são avaliados na ordem definida por esse campo. Assim que uma correspondência for encontrada, nenhum atributo correspondente será avaliado.

   * **Aplicar este mapeamento**

     * **Sempre** – aplicar esse mapeamento nas ações de criação e atualização do usuário

     * **Somente durante a criação** -aplique este mapeamento somente em ações de criação de usuário

6. Para salvar seus mapeamentos, clique em **salvar** na parte superior da seção de mapeamento de atributos.

Depois que a configuração de mapeamento de atributo for concluída, agora você poderá [habilitar e iniciar o serviço de provisionamento de usuário](#enable-and-launch-user-provisioning).

## <a name="configuring-azure-ad-attribute-writeback-to-workday"></a>Configurando o Write-back de atributo do Azure AD para workday

Siga estas instruções para configurar o Write-back de endereços de email do usuário e nome de usuário de Azure Active Directory para workday.

* [Adicionando o aplicativo conector de write-back e criando a conexão com o workday](#part-1-adding-the-writeback-connector-app-and-creating-the-connection-to-workday)
* [Configurar mapeamentos de atributo de write-back](#part-2-configure-writeback-attribute-mappings)
* [Habilitar e iniciar o provisionamento de usuário](#enable-and-launch-user-provisioning)

### <a name="part-1-adding-the-writeback-connector-app-and-creating-the-connection-to-workday"></a>Parte 1: adicionando o aplicativo do conector de write-back e criando a conexão com o workday

**Para configurar o conector de write-back do workday:**

1. Ir para <https://portal.azure.com>

2. Na barra de navegação à esquerda, selecione **Azure Active Directory**

3. Selecione **aplicativos empresariais**e **todos os aplicativos**.

4. Selecione **Adicionar um aplicativo**e, em seguida, selecione a categoria **tudo** .

5. Pesquise por **write-back do workday**e adicione esse aplicativo da galeria.

6. Depois que o aplicativo for adicionado e a tela de detalhes do aplicativo for exibida, selecione **provisionamento**

7. Alterar o **modo** de provisionamento para **automático**

8. Conclua a seção de **credenciais de administrador** da seguinte maneira:

   * **Nome de usuário do administrador** – Insira o nome de usuário da conta do sistema de integração do workday, com o nome de domínio do locatário anexado. Deve ser semelhante a: *username\@contoso4*

   * **Senha de administrador –** Insira a senha da conta do sistema de integração do workday

   * **URL do locatário –** Insira a URL para o ponto de extremidade de serviços Web do WORKDAY para seu locatário. Esse valor deve ser semelhante a: https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources, em que *contoso4* é substituído pelo nome do locatário correto e *WD3-impl* é substituído pela cadeia de caracteres de ambiente correta (se necessário).

   * **Email de notificação –** Insira seu endereço de email e marque a caixa de seleção "enviar email se ocorrer falha".

   * Clique no botão **testar conexão** . Se o teste de conexão tiver sucesso, clique no botão **salvar** na parte superior. Se falhar, verifique se a URL e as credenciais do workday são válidas no workday.

### <a name="part-2-configure-writeback-attribute-mappings"></a>Parte 2: configurar mapeamentos de atributo de write-back

Nesta seção, você configurará como os atributos de write-back fluem do Azure AD para o workday. No momento, o conector só dá suporte a Write-back de endereço de email e nome de usuário para workday.

1. Na guia provisionamento em **mapeamentos**, clique em **sincronizar Azure Active Directory usuários para o workday**.

2. No campo **escopo do objeto de origem** , você pode, opcionalmente, filtrar, quais conjuntos de usuários no Azure Active Directory devem ter seus endereços de email gravados no workday. O escopo padrão é "todos os usuários no Azure AD".

3. Na seção **mapeamentos de atributo** , atualize a ID correspondente para indicar o atributo em Azure Active Directory em que a ID do funcionário ou a ID de funcionários do workday está armazenada. Um método de correspondência popular é sincronizar a ID de funcionário ou a ID do trabalhador do workday com o extensionAttribute1-15 no Azure AD e, em seguida, usar esse atributo no Azure AD para corresponder os usuários de volta no workday.

4. Normalmente, você mapeia o atributo *userPrincipalName* do Azure ad para o atributo workday *userid* e mapeia o atributo de *email* do Azure ad para o atributo *EmailAddress* do workday. Para salvar seus mapeamentos, clique em **salvar** na parte superior da seção de mapeamento de atributos.

Depois que a configuração de mapeamento de atributo for concluída, agora você poderá [habilitar e iniciar o serviço de provisionamento de usuário](#enable-and-launch-user-provisioning).

## <a name="enable-and-launch-user-provisioning"></a>Habilitar e iniciar o provisionamento de usuário

Depois que as configurações do aplicativo de provisionamento do workday tiverem sido concluídas, você poderá ativar o serviço de provisionamento no portal do Azure.

> [!TIP]
> Por padrão, quando você ativa o serviço de provisionamento, ele inicia as operações de provisionamento para todos os usuários no escopo. Se houver erros no mapeamento ou nos problemas de dados do workday, o trabalho de provisionamento poderá falhar e entrar no estado de quarentena. Para evitar isso, como uma prática recomendada, é recomendável configurar o filtro de **escopo do objeto de origem** e testar os mapeamentos de atributo com alguns usuários de teste antes de iniciar a sincronização completa para todos os usuários. Depois de verificar se os mapeamentos funcionam e estão fornecendo os resultados desejados, você pode remover o filtro ou expandi-lo gradualmente para incluir mais usuários.

1. Na guia **provisionamento** , defina o **status de provisionamento** como **ativado**.

2. Clique em **Guardar**.

3. Esta operação iniciará a sincronização inicial, o que pode levar um número variável de horas, dependendo de quantos usuários estiverem no locatário do workday. 

4. A qualquer momento, verifique a guia **logs de auditoria** no portal do Azure para ver quais ações o serviço de provisionamento executou. Os logs de auditoria listam todos os eventos de sincronização individuais executados pelo serviço de provisionamento, como quais usuários estão sendo lidos do workday e, posteriormente, adicionados ou atualizados para Active Directory. Consulte a seção de solução de problemas para obter instruções sobre como examinar os logs de auditoria e corrigir os erros de provisionamento.

5. Depois que a sincronização inicial for concluída, ela gravará um relatório de Resumo de auditoria na guia **provisionamento** , conforme mostrado abaixo.

   ![Portal do Azure](./media/workday-inbound-tutorial/wd_3.png)

## <a name="frequently-asked-questions-faq"></a>Perguntas Mais Frequentes (FAQ)

* **Perguntas de capacidade da solução**
  * [Ao processar uma nova contratação do workday, como a solução define a senha da nova conta de usuário no Active Directory?](#when-processing-a-new-hire-from-workday-how-does-the-solution-set-the-password-for-the-new-user-account-in-active-directory)
  * [A solução dá suporte ao envio de notificações por email após a conclusão das operações de provisionamento?](#does-the-solution-support-sending-email-notifications-after-provisioning-operations-complete)
  * [Como fazer gerenciar a entrega de senhas para novas contratações e fornecer com segurança um mecanismo para redefinir sua senha?](#how-do-i-manage-delivery-of-passwords-for-new-hires-and-securely-provide-a-mechanism-to-reset-their-password)
  * [A solução armazena em cache os perfis de usuário do workday na nuvem do Azure AD ou na camada do agente de provisionamento?](#does-the-solution-cache-workday-user-profiles-in-the-azure-ad-cloud-or-at-the-provisioning-agent-layer)
  * [A solução dá suporte à atribuição de grupos do AD locais para o usuário?](#does-the-solution-support-assigning-on-premises-ad-groups-to-the-user)
  * [Quais APIs do workday a solução usa para consultar e atualizar os perfis de trabalho do workday?](#which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles)
  * [Posso configurar meu locatário do workday HCM com dois locatários do Azure AD?](#can-i-configure-my-workday-hcm-tenant-with-two-azure-ad-tenants)
  * [Por que o aplicativo de provisionamento de usuário "WORKDAY para o Azure AD" não tem suporte se implantamos Azure AD Connect?](#why-workday-to-azure-ad-user-provisioning-app-is-not-supported-if-we-have-deployed-azure-ad-connect)
  * [Como fazer sugerir melhorias ou solicitar novos recursos relacionados ao workday e à integração do Azure AD?](#how-do-i-suggest-improvements-or-request-new-features-related-to-workday-and-azure-ad-integration)

* **Perguntas do agente de provisionamento**
  * [O que é a versão GA do agente de provisionamento?](#what-is-the-ga-version-of-the-provisioning-agent)
  * [Como fazer saber a versão do meu agente de provisionamento?](#how-do-i-know-the-version-of-my-provisioning-agent)
  * [A Microsoft envia automaticamente as atualizações do agente de provisionamento?](#does-microsoft-automatically-push-provisioning-agent-updates)
  * [Posso instalar o agente de provisionamento no mesmo servidor que executa o Azure AD Connect?](#can-i-install-the-provisioning-agent-on-the-same-server-running-azure-ad-connect)
  * [Como fazer configurar o agente de provisionamento para usar um servidor proxy para a comunicação HTTP de saída?](#how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication)
  * [Como fazer garantir que o agente de provisionamento seja capaz de se comunicar com o locatário do Azure AD e que nenhum firewall esteja bloqueando as portas exigidas pelo agente?](#how-do-i-ensure-that-the-provisioning-agent-is-able-to-communicate-with-the-azure-ad-tenant-and-no-firewalls-are-blocking-ports-required-by-the-agent)
  * [Como fazer de registrar o domínio associado ao meu agente de provisionamento?](#how-do-i-de-register-the-domain-associated-with-my-provisioning-agent)
  * [Como fazer desinstalar o agente de provisionamento?](#how-do-i-uninstall-the-provisioning-agent)
  
* **Perguntas sobre configuração e mapeamento de atributo WORKDAY para AD**
  * [Como fazer fazer backup ou exportar uma cópia de trabalho do meu esquema e mapeamento de atributos de provisionamento do workday?](#how-do-i-back-up-or-export-a-working-copy-of-my-workday-provisioning-attribute-mapping-and-schema)
  * [Tenho atributos personalizados no workday e Active Directory. Como fazer configurar a solução para trabalhar com meus atributos personalizados?](#i-have-custom-attributes-in-workday-and-active-directory-how-do-i-configure-the-solution-to-work-with-my-custom-attributes)
  * [Posso provisionar a foto do usuário do WORKDAY para Active Directory?](#can-i-provision-users-photo-from-workday-to-active-directory)
  * [Como fazer sincronizar números de celular do workday com base no consentimento do usuário para uso público?](#how-do-i-sync-mobile-numbers-from-workday-based-on-user-consent-for-public-usage)
  * [Como fazer nomes de exibição de formato no AD com base nos atributos de departamento/país/cidade do usuário e manipulam variações regionais?](#how-do-i-format-display-names-in-ad-based-on-the-users-departmentcountrycity-attributes-and-handle-regional-variances)
  * [Como posso usar SelectUniqueValue para gerar valores exclusivos para o atributo samAccountName?](#how-can-i-use-selectuniquevalue-to-generate-unique-values-for-samaccountname-attribute)
  * [Como fazer remover caracteres com sinais diacríticos e convertê-los em alfabetos normais em inglês?](#how-do-i-remove-characters-with-diacritics-and-convert-them-into-normal-english-alphabets)

### <a name="solution-capability-questions"></a>Perguntas de capacidade da solução

#### <a name="when-processing-a-new-hire-from-workday-how-does-the-solution-set-the-password-for-the-new-user-account-in-active-directory"></a>Ao processar uma nova contratação do workday, como a solução define a senha da nova conta de usuário no Active Directory?

Quando o agente de provisionamento local recebe uma solicitação para criar uma nova conta do AD, ele gera automaticamente uma senha aleatória complexa criada para atender aos requisitos de complexidade de senha definidos pelo servidor do AD e define isso no objeto de usuário. Essa senha não é registrada em nenhum lugar.

#### <a name="does-the-solution-support-sending-email-notifications-after-provisioning-operations-complete"></a>A solução dá suporte ao envio de notificações por email após a conclusão das operações de provisionamento?

Não, o envio de notificações por email após a conclusão das operações de provisionamento não tem suporte na versão atual.

#### <a name="how-do-i-manage-delivery-of-passwords-for-new-hires-and-securely-provide-a-mechanism-to-reset-their-password"></a>Como fazer gerenciar a entrega de senhas para novas contratações e fornecer com segurança um mecanismo para redefinir sua senha?

Uma das etapas finais envolvidas no novo provisionamento de conta do AD é a entrega da senha temporária atribuída à conta do AD do usuário. Muitas empresas ainda usam a abordagem tradicional de fornecer a senha temporária para o gerente do usuário, que, em seguida, passa a senha para o novo trabalho de contratação/contingente. Esse processo tem uma falha de segurança inerente e há uma opção disponível para implementar uma abordagem melhor usando os recursos do Azure AD.

Como parte do processo de contratação, as equipes de RH geralmente executam uma verificação em segundo plano e examinaremos o número de celular da nova contratação. Com o WORKDAY para integração de provisionamento de usuário do AD, você pode criar sobre esse fato e distribuir um recurso de redefinição de senha de autoatendimento para o usuário no dia 1. Isso é feito propagando o atributo "número do celular" da nova contratação do WORKDAY para o AD e, em seguida, do AD para o Azure AD usando o Azure AD Connect. Depois que o "número de celular" estiver presente no Azure AD, você poderá habilitar a [SSPR (redefinição de senha de autoatendimento)](../authentication/howto-sspr-authenticationdata.md) para a conta do usuário, de modo que no dia 1, uma nova contratação poderá usar o número de celular registrado e verificado para autenticação.

#### <a name="does-the-solution-cache-workday-user-profiles-in-the-azure-ad-cloud-or-at-the-provisioning-agent-layer"></a>A solução armazena em cache os perfis de usuário do workday na nuvem do Azure AD ou na camada do agente de provisionamento?

Não, a solução não mantém um cache de perfis de usuário. O serviço de provisionamento do Azure AD simplesmente atua como um processador de dados, lendo dados do workday e gravando no Active Directory de destino ou no Azure AD. Consulte a seção [Gerenciando dados pessoais](#managing-personal-data) para obter detalhes relacionados à privacidade do usuário e à retenção de dados.

#### <a name="does-the-solution-support-assigning-on-premises-ad-groups-to-the-user"></a>A solução dá suporte à atribuição de grupos do AD locais para o usuário?

Não há suporte para essa funcionalidade no momento. A solução alternativa recomendada é implantar um script do PowerShell que consulta o ponto de extremidade de API do Graph do Azure AD para obter dados de log de auditoria e usá-lo para disparar cenários como atribuição de grupo. Esse script do PowerShell pode ser anexado a um Agendador de tarefas e implantado na mesma caixa que executa o agente de provisionamento.  

#### <a name="which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles"></a>Quais APIs do workday a solução usa para consultar e atualizar os perfis de trabalho do workday?

Atualmente, a solução usa as seguintes APIs do workday:

* Get_Workers (v 21.1) para buscar informações de trabalho
* Maintain_Contact_Information (v 26.1) para o recurso de write-back de email de trabalho
* Update_Workday_Account (v 31.2) para o recurso de write-back de nome de usuário

#### <a name="can-i-configure-my-workday-hcm-tenant-with-two-azure-ad-tenants"></a>Posso configurar meu locatário do workday HCM com dois locatários do Azure AD?

Sim, há suporte para essa configuração. Aqui estão as etapas de alto nível para configurar este cenário:

* Implante o agente de provisionamento #1 e registre-o com o #1 de locatário do Azure AD.
* Implante o agente de provisionamento #2 e registre-o com o #2 de locatário do Azure AD.
* Com base nos "domínios filho" que cada agente de provisionamento gerenciará, configure cada agente com os domínios. Um agente pode lidar com vários domínios.
* No portal do Azure, configure o WORKDAY para o aplicativo de provisionamento de usuário do AD em cada locatário e configurar-o com os respectivos domínios.

#### <a name="why-workday-to-azure-ad-user-provisioning-app-is-not-supported-if-we-have-deployed-azure-ad-connect"></a>Por que o aplicativo de provisionamento de usuário "WORKDAY para o Azure AD" não tem suporte se implantamos Azure AD Connect?

Quando o AD do Azure é usado no modo híbrido (onde ele contém uma combinação de nuvem + usuários locais), é importante ter uma definição clara de "origem de autoridade". Normalmente, os cenários híbridos exigem a implantação de Azure AD Connect. Quando Azure AD Connect é implantado, o AD local é a origem da autoridade. A introdução da workday ao Azure AD Connector na combinação pode levar a uma situação em que os valores de atributo workday poderiam substituir os valores definidos por Azure AD Connect. Portanto, o uso do aplicativo de provisionamento "WORKDAY para o Azure AD" não tem suporte quando Azure AD Connect está habilitado. Em tais situações, é recomendável usar o aplicativo de provisionamento "WORKDAY para o usuário do AD" para colocar os usuários no AD local e, em seguida, sincronizá-los no Azure AD usando o Azure AD Connect.

#### <a name="how-do-i-suggest-improvements-or-request-new-features-related-to-workday-and-azure-ad-integration"></a>Como fazer sugerir melhorias ou solicitar novos recursos relacionados ao workday e à integração do Azure AD?

Seus comentários são altamente valorizados, pois eles nos ajudam a definir a direção para as versões e os aprimoramentos futuros. Agradecemos todos os comentários e incentivamos você a enviar sua sugestão de ideias ou melhorias no [Fórum de comentários do Azure ad](https://feedback.azure.com/forums/169401-azure-active-directory). Para obter comentários específicos relacionados à integração do workday, selecione a categoria *aplicativos SaaS* e pesquise usando o *workday* das palavras-chave para encontrar comentários existentes relacionados ao workday.

![Aplicativos SaaS UserVoice](media/workday-inbound-tutorial/uservoice_saas_apps.png)

![Workday do UserVoice](media/workday-inbound-tutorial/uservoice_workday_feedback.png)

Ao sugerir uma nova ideia, verifique se outra pessoa já sugeriu um recurso semelhante. Nesse caso, você pode votar no recurso ou na solicitação de aprimoramento. Você também pode deixar um comentário sobre seu caso de uso específico para mostrar o suporte para a ideia e demonstrar como o recurso será valioso para você também.

### <a name="provisioning-agent-questions"></a>Perguntas do agente de provisionamento

#### <a name="what-is-the-ga-version-of-the-provisioning-agent"></a>O que é a versão GA do agente de provisionamento?

* A versão GA do agente de provisionamento é 1.1.30 e superior.
* Se a versão do agente for menor que 1.1.30, você estará executando a versão de visualização pública e ela será atualizada automaticamente para a versão GA se o servidor que hospeda o agente tiver o tempo de execução do .NET 4.7.1.
  * Você pode [verificar a versão do .net](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) instalada no servidor. Se o servidor não estiver executando o .NET 4.7.1, você poderá [baixar e instalar o .NET 4.7.1](https://support.microsoft.com/help/4033342/the-net-framework-4-7-1-offline-installer-for-windows). O agente de provisionamento será atualizado automaticamente para a versão GA após a instalação do .NET 4.7.1.

#### <a name="how-do-i-know-the-version-of-my-provisioning-agent"></a>Como fazer saber a versão do meu agente de provisionamento?

* Entre no Windows Server onde o agente de provisionamento está instalado.
* Ir para o **painel de controle** -> **desinstalar ou alterar um** menu do programa
* Procure a versão correspondente à entrada **Microsoft Azure ad conectar o agente de provisionamento**

  ![Portal do Azure](./media/workday-inbound-tutorial/pa_version.png)

#### <a name="does-microsoft-automatically-push-provisioning-agent-updates"></a>A Microsoft envia automaticamente as atualizações do agente de provisionamento?

Sim, a Microsoft atualiza automaticamente o agente de provisionamento. Você pode desabilitar as atualizações automáticas parando o serviço Windows **Microsoft Azure ad o atualizador do agente do Connect**.

#### <a name="can-i-install-the-provisioning-agent-on-the-same-server-running-azure-ad-connect"></a>Posso instalar o agente de provisionamento no mesmo servidor que executa o Azure AD Connect?

Sim, você pode instalar o agente de provisionamento no mesmo servidor que executa o Azure AD Connect.

#### <a name="at-the-time-of-configuration-the-provisioning-agent-prompts-for-azure-ad-admin-credentials-does-the-agent-store-the-credentials-locally-on-the-server"></a>No momento da configuração, o agente de provisionamento solicita as credenciais de administrador do Azure AD. O agente armazena as credenciais localmente no servidor?

Durante a configuração, o agente de provisionamento solicita as credenciais de administrador do Azure AD somente para se conectar ao seu locatário do Azure AD. Ele não armazena as credenciais localmente no servidor. No entanto, ele mantém as credenciais usadas para se conectar ao *domínio de Active Directory* local em um cofre de senha do Windows.

#### <a name="how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication"></a>Como fazer configurar o agente de provisionamento para usar um servidor proxy para a comunicação HTTP de saída?

O agente de provisionamento dá suporte ao uso de proxy de saída. Você pode configurá-lo editando o arquivo de configuração do agente **C:\Program Files\Microsoft Azure ad Connect Provisionando Agent\AADConnectProvisioningAgent.exe.config**. Adicione as seguintes linhas a ela, até o final do arquivo antes da marca de fechamento `</configuration>`.
Substitua as variáveis [Proxy-Server] e [proxy-Port] pelo nome do servidor proxy e pelos valores de porta.

```xml
    <system.net>
          <defaultProxy enabled="true" useDefaultCredentials="true">
             <proxy
                usesystemdefault="true"
                proxyaddress="http://[proxy-server]:[proxy-port]"
                bypassonlocal="true"
             />
         </defaultProxy>
    </system.net>
```

#### <a name="how-do-i-ensure-that-the-provisioning-agent-is-able-to-communicate-with-the-azure-ad-tenant-and-no-firewalls-are-blocking-ports-required-by-the-agent"></a>Como fazer garantir que o agente de provisionamento seja capaz de se comunicar com o locatário do Azure AD e que nenhum firewall esteja bloqueando as portas exigidas pelo agente?

Você também pode verificar se você tem todas as portas necessárias abertas abrindo a [ferramenta de teste de portas de conector](https://aadap-portcheck.connectorporttest.msappproxy.net/) de sua rede local. Mais marcas de verificação verde significa maior resiliência.

Para certificar-se de que a ferramenta dá-lhe os resultados certos, certifique-se de que para:

* Abra a ferramenta em um navegador do servidor no qual você instalou o agente de provisionamento.
* Certifique-se de que quaisquer proxies ou firewalls aplicáveis ao agente de provisionamento também sejam aplicados a esta página. Isso pode ser feito no Internet Explorer acessando **configurações-> opções da Internet-> conexões-> configurações de LAN**. Nessa página, você verá o campo "usar um servidor proxy para sua LAN". Selecione essa caixa e coloque o endereço do proxy no campo "endereço".

#### <a name="can-one-provisioning-agent-be-configured-to-provision-multiple-ad-domains"></a>Um agente de provisionamento pode ser configurado para provisionar vários domínios do AD?

Sim, um agente de provisionamento pode ser configurado para lidar com vários domínios do AD, contanto que o agente tenha a linha de visão para os controladores de domínio respectivos. A Microsoft recomenda configurar um grupo de 3 agentes de provisionamento que atendem o mesmo conjunto de domínios do AD para garantir a alta disponibilidade e fornecer suporte ao failover.

#### <a name="how-do-i-de-register-the-domain-associated-with-my-provisioning-agent"></a>Como fazer de registrar o domínio associado ao meu agente de provisionamento?

* No portal do Azure, obtenha a *ID de locatário* do seu locatário do Azure AD.
* Entre no Windows Server que executa o agente de provisionamento.
* Abra o PowerShell como administrador do Windows.
* Altere para o diretório que contém os scripts de registro e execute os comandos a seguir, substituindo a ID de locatário \[\] parâmetro pelo valor da sua ID de locatário.

  ```powershell
  cd “C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\RegistrationPowershell\Modules\PSModulesFolder”
  Import-Module "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\RegistrationPowershell\Modules\PSModulesFolder\AppProxyPSModule.psd1"
  Get-PublishedResources -TenantId "[tenant ID]"
  ```

* Na lista de agentes que aparecem – Copie o valor do campo "ID" desse recurso cujo *resourceName* é igual ao seu nome de domínio do AD.
* Cole o valor de ID nesse comando e execute o comando no PowerShell.

  ```powershell
  Remove-PublishedResource -ResourceId "[resource ID]" -TenantId "[tenant ID]"
  ```

* Execute novamente o assistente de configuração do agente.
* Todos os outros agentes que foram atribuídos anteriormente a esse domínio precisarão ser reconfigurados.

#### <a name="how-do-i-uninstall-the-provisioning-agent"></a>Como fazer desinstalar o agente de provisionamento?

* Entre no Windows Server onde o agente de provisionamento está instalado.
* Ir para o **painel de controle** -> **desinstalar ou alterar um** menu do programa
* Desinstale os seguintes programas:
  * Agente de provisionamento do Microsoft Azure AD Connect
  * Atualizador do agente do Microsoft Azure AD Connect
  * Pacote de agente de provisionamento do Microsoft Azure AD Connect

### <a name="workday-to-ad-attribute-mapping-and-configuration-questions"></a>Perguntas sobre configuração e mapeamento de atributo WORKDAY para AD

#### <a name="how-do-i-back-up-or-export-a-working-copy-of-my-workday-provisioning-attribute-mapping-and-schema"></a>Como fazer fazer backup ou exportar uma cópia de trabalho do meu esquema e mapeamento de atributos de provisionamento do workday?

Você pode usar Microsoft Graph API para exportar a configuração de provisionamento de usuário do workday. Consulte as etapas na seção [exportando e importando sua configuração de mapeamento de atributo de provisionamento de usuário do workday](#exporting-and-importing-your-configuration) para obter detalhes.

#### <a name="i-have-custom-attributes-in-workday-and-active-directory-how-do-i-configure-the-solution-to-work-with-my-custom-attributes"></a>Tenho atributos personalizados no workday e Active Directory. Como fazer configurar a solução para trabalhar com meus atributos personalizados?

A solução dá suporte a atributos personalizados de workday e Active Directory. Para adicionar seus atributos personalizados ao esquema de mapeamento, abra a folha **mapeamento de atributo** e role para baixo até expandir a seção **Mostrar opções avançadas**. 

![Editar lista de atributos](./media/workday-inbound-tutorial/wd_edit_attr_list.png)

Para adicionar seus atributos de workday personalizados, selecione a opção *Editar lista de atributos para workday* e adicione seus atributos personalizados do AD, selecione a opção *Editar lista de atributos para o local Active Directory*.

Veja também:

* [Personalizando a lista de atributos de usuário do workday](#customizing-the-list-of-workday-user-attributes)

#### <a name="how-do-i-configure-the-solution-to-only-update-attributes-in-ad-based-on-workday-changes-and-not-create-any-new-ad-accounts"></a>Como fazer configurar a solução para atualizar apenas os atributos no AD com base nas alterações do workday e não criar novas contas do AD?

Essa configuração pode ser obtida definindo as **ações do objeto de destino** na folha **mapeamentos de atributo** , conforme mostrado abaixo:

![Atualizar ação](./media/workday-inbound-tutorial/wd_target_update_only.png)

Marque a caixa de seleção "atualizar" para que apenas as operações de atualização fluam do WORKDAY para o AD. 

#### <a name="can-i-provision-users-photo-from-workday-to-active-directory"></a>Posso provisionar a foto do usuário do WORKDAY para Active Directory?

Atualmente, a solução não dá suporte à definição de atributos binários, como *ThumbNailPhoto* e *JpegPhoto* em Active Directory.

#### <a name="how-do-i-sync-mobile-numbers-from-workday-based-on-user-consent-for-public-usage"></a>Como fazer sincronizar números de celular do workday com base no consentimento do usuário para uso público?

* Vá para a folha "provisionamento" do seu aplicativo de provisionamento do workday.
* Clique nos mapeamentos de atributo 
* Em **mapeamentos**, selecione **sincronizar trabalhos do WORKDAY para o local Active Directory** (ou **sincronizar trabalhos do workday com o Azure ad**).
* Na página Mapeamentos de atributo, role para baixo e marque a caixa "Mostrar opções avançadas".  Clique em **Editar lista de atributos para workday**
* Na folha que é aberta, localize o atributo "Mobile" e clique na linha para que você possa editar a **expressão de API** ![Mobile GDPR](./media/workday-inbound-tutorial/mobile_gdpr.png)

* Substitua a **expressão de API** pela nova expressão a seguir, que recupera o número de celular do trabalho somente se o "sinalizador de uso público" estiver definido como "true" no workday.

    ```
     wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Contact_Data/wd:Phone_Data[translate(string(wd:Phone_Device_Type_Reference/@wd:Descriptor),'abcdefghijklmnopqrstuvwxyz','ABCDEFGHIJKLMNOPQRSTUVWXYZ')='MOBILE' and translate(string(wd:Usage_Data/wd:Type_Data/wd:Type_Reference/@wd:Descriptor),'abcdefghijklmnopqrstuvwxyz','ABCDEFGHIJKLMNOPQRSTUVWXYZ')='WORK' and string(wd:Usage_Data/@wd:Public)='1']/@wd:Formatted_Phone
    ```

* Salve a lista de atributos.
* Salve o mapeamento de atributo.
* Desmarque o estado atual e reinicie a sincronização completa.

#### <a name="how-do-i-format-display-names-in-ad-based-on-the-users-departmentcountrycity-attributes-and-handle-regional-variances"></a>Como fazer nomes de exibição de formato no AD com base nos atributos de departamento/país/cidade do usuário e manipulam variações regionais?

É um requisito comum para configurar o atributo *DisplayName* no AD para que ele também forneça informações sobre o departamento e o país/região do usuário. Por exemplo, se John Smith trabalha no departamento de marketing nos EUA, talvez você queira que seu *DisplayName* apareça como *Smith, John (marketing-US)* .

Veja como você pode lidar com esses requisitos para construir *CN* ou *DisplayName* para incluir atributos como empresa, unidade de negócios, cidade ou país/região.

* Cada atributo workday é recuperado usando uma expressão de API XPATH subjacente, que é configurável na **seção de mapeamento de atributos-> avançado-> editar lista de atributos para o workday**. Aqui está a expressão de API XPATH padrão para os atributos workday *nomepreferido*, *PreferredLastName*, *Company* e *SupervisoryOrganization* .

     | Atributo workday | Expressão XPATH da API |
     | ----------------- | -------------------- |
     | Nomepreferido | WD: Worker/WD: Worker_Data/WD: Personal_Data/WD: Name_Data/WD: Preferred_Name_Data/WD: Name_Detail_Data/WD: First_Name/Text () |
     | PreferredLastName | WD: Worker/WD: Worker_Data/WD: Personal_Data/WD: Name_Data/WD: Preferred_Name_Data/WD: Name_Detail_Data/WD: Last_Name/Text () |
     | Empresa | WD: Worker/WD: Worker_Data/WD: Organization_Data/WD: Worker_Organization_Data [WD: Organization_Data/WD: Organization_Type_Reference/WD: ID [@wd:type= ' Organization_Type_ID '] = ' Company ']/wd:Organization_Reference/@wd:Descriptor |
     | SupervisoryOrganization | WD: Worker/WD: Worker_Data/WD: Organization_Data/WD: Worker_Organization_Data/WD: Organization_Data [WD: Organization_Type_Reference/WD: ID [@wd:type= ' Organization_Type_ID '] = ' supervisor ']/WD: Organization_Name/Text () |
  
   Confirme com sua equipe do workday que a expressão de API acima é válida para sua configuração de locatário do workday. Se necessário, você pode editá-los conforme descrito na seção [Personalizando a lista de atributos de usuário do workday](#customizing-the-list-of-workday-user-attributes).

* Da mesma forma, as informações de país presentes no workday são recuperadas usando o seguinte XPATH: *WD: Worker/WD: Worker_Data/WD: Employment_Data/WD: Position_Data/WD: Business_Site_Summary_Data/WD: Address_Data/WD: Country_Reference*

     Há cinco atributos relacionados ao país que estão disponíveis na seção da lista de atributos do workday.

     | Atributo workday | Expressão XPATH da API |
     | ----------------- | -------------------- |
     | CountryReference | WD: Worker/WD: Worker_Data/WD: Employment_Data/WD: Position_Data/WD: Business_Site_Summary_Data/WD: Address_Data/WD: Country_Reference/WD: ID [@wd:type= ' ISO_3166-1_Alpha-3_Code ']/Text () |
     | CountryReferenceFriendly | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/@wd:Descriptor |
     | CountryReferenceNumeric | WD: Worker/WD: Worker_Data/WD: Employment_Data/WD: Position_Data/WD: Business_Site_Summary_Data/WD: Address_Data/WD: Country_Reference/WD: ID [@wd:type= ' ISO_3166-1_Numeric-3_Code ']/Text () |
     | CountryReferenceTwoLetter | WD: Worker/WD: Worker_Data/WD: Employment_Data/WD: Position_Data/WD: Business_Site_Summary_Data/WD: Address_Data/WD: Country_Reference/WD: ID [@wd:type= ' ISO_3166-1_Alpha-2_Code ']/Text () |
     | CountryRegionReference | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Region_Reference/@wd:Descriptor |

  Confirme com sua equipe do workday que as expressões de API acima são válidas para sua configuração de locatário do workday. Se necessário, você pode editá-los conforme descrito na seção [Personalizando a lista de atributos de usuário do workday](#customizing-the-list-of-workday-user-attributes).

* Para criar a expressão de mapeamento de atributo correta, identifique qual atributo workday "autoritativamente" representa o nome do usuário, o sobrenome, o país/região e o departamento. Digamos que os atributos sejam *nomepreferido*, *PreferredLastName*, *CountryReferenceTwoLetter* e *SupervisoryOrganization* , respectivamente. Você pode usar isso para criar uma expressão para o atributo *DisplayName* do AD da seguinte maneira para obter um nome de exibição como *Smith, John (marketing-US)* .

    ```
     Append(Join(", ",[PreferredLastName],[PreferredFirstName]), Join(""," (",[SupervisoryOrganization],"-",[CountryReferenceTwoLetter],")"))
    ```
    Depois de ter a expressão correta, edite a tabela mapeamentos de atributo e modifique o mapeamento de atributo *DisplayName* , conforme mostrado abaixo: ![o mapeamento DisplayName](./media/workday-inbound-tutorial/wd_displayname_map.png)

* Estendendo o exemplo acima, digamos que você queira converter nomes de cidades provenientes do workday em valores abreviados e usá-los para criar nomes de exibição como *Smith, João (Chi)* ou *Doe, Jane (NYC)* e, em seguida, esse resultado pode ser obtido usando uma expressão de switch com o atributo workday *Municipal* como a variável determinante.

     ```
    Switch
    (
      [Municipality],
      Join(", ", [PreferredLastName], [PreferredFirstName]),  
           "Chicago", Append(Join(", ",[PreferredLastName], [PreferredFirstName]), "(CHI)"),
           "New York", Append(Join(", ",[PreferredLastName], [PreferredFirstName]), "(NYC)"),
           "Phoenix", Append(Join(", ",[PreferredLastName], [PreferredFirstName]), "(PHX)")
    )
     ```
    Veja também:
  * [Alterar sintaxe de função](../manage-apps/functions-for-customizing-application-data.md#switch)
  * [Sintaxe da função de junção](../manage-apps/functions-for-customizing-application-data.md#join)
  * [Sintaxe da função Append](../manage-apps/functions-for-customizing-application-data.md#append)

#### <a name="how-can-i-use-selectuniquevalue-to-generate-unique-values-for-samaccountname-attribute"></a>Como posso usar SelectUniqueValue para gerar valores exclusivos para o atributo samAccountName?

Digamos que você deseja gerar valores exclusivos para o atributo *sAMAccountName* usando uma combinação de atributos *FirstName* e *LastName* do workday. Veja abaixo uma expressão com a qual você pode começar:

```
SelectUniqueValue(
    Replace(Mid(Replace(NormalizeDiacritics(StripSpaces(Join("",  Mid([FirstName],1,1), [LastName]))), , "([\\/\\\\\\[\\]\\:\\;\\|\\=\\,\\+\\*\\?\\<\\>])", , "", , ), 1, 20), , "(\\.)*$", , "", , ),
    Replace(Mid(Replace(NormalizeDiacritics(StripSpaces(Join("",  Mid([FirstName],1,2), [LastName]))), , "([\\/\\\\\\[\\]\\:\\;\\|\\=\\,\\+\\*\\?\\<\\>])", , "", , ), 1, 20), , "(\\.)*$", , "", , ),
    Replace(Mid(Replace(NormalizeDiacritics(StripSpaces(Join("",  Mid([FirstName],1,3), [LastName]))), , "([\\/\\\\\\[\\]\\:\\;\\|\\=\\,\\+\\*\\?\\<\\>])", , "", , ), 1, 20), , "(\\.)*$", , "", , )
)
```

Como a expressão acima funciona: se o usuário for John Smith, ele primeiro tentará gerar JSmith, se o JSmith já existir, ele gerará JoSmith, se existir, gerará JohSmith. A expressão também garante que o valor gerado atenda à restrição de comprimento e à restrição de caracteres especiais associada ao *sAMAccountName*.

Veja também:

* [Sintaxe de função mid](../manage-apps/functions-for-customizing-application-data.md#mid)
* [Substituir sintaxe da função](../manage-apps/functions-for-customizing-application-data.md#replace)
* [Sintaxe da função SelectUniqueValue](../manage-apps/functions-for-customizing-application-data.md#selectuniquevalue)

#### <a name="how-do-i-remove-characters-with-diacritics-and-convert-them-into-normal-english-alphabets"></a>Como fazer remover caracteres com sinais diacríticos e convertê-los em alfabetos normais em inglês?

Use a função [NormalizeDiacritics](../manage-apps/functions-for-customizing-application-data.md#normalizediacritics) para remover caracteres especiais em nome e sobrenome do usuário, ao construir o endereço de email ou o valor CN para o usuário.

## <a name="troubleshooting-tips"></a>Sugestões de resolução de problemas

Esta seção fornece diretrizes específicas sobre como solucionar problemas de provisionamento com sua integração do workday usando os logs de auditoria do Azure AD e os logs de Visualizador de Eventos do Windows Server. Ele se baseia nas etapas e conceitos de solução de problemas genéricos capturados no [tutorial: relatórios sobre o provisionamento automático de conta de usuário](../manage-apps/check-status-user-account-provisioning.md)

Esta seção aborda os seguintes aspectos da solução de problemas:

* [Configurando o Windows Visualizador de Eventos para solução de problemas do agente](#setting-up-windows-event-viewer-for-agent-troubleshooting)
* [Configurando portal do Azure logs de auditoria para solução de problemas de serviço](#setting-up-azure-portal-audit-logs-for-service-troubleshooting)
* [Noções básicas sobre logs para operações de criação de conta de usuário do AD](#understanding-logs-for-ad-user-account-create-operations)
* [Noções básicas sobre logs para operações de atualização do Gerenciador](#understanding-logs-for-manager-update-operations)
* [Resolvendo erros frequentemente encontrados](#resolving-commonly-encountered-errors)

### <a name="setting-up-windows-event-viewer-for-agent-troubleshooting"></a>Configurando o Windows Visualizador de Eventos para solução de problemas do agente

* Entre no computador do Windows Server em que o agente de provisionamento está implantado
* Abra o aplicativo de área de trabalho **do Windows Server Visualizador de eventos** .
* Selecione **logs do Windows > aplicativo**.
* Usar o **log atual do filtro...** opção para exibir todos os eventos registrados no AAD de origem **. Conecte-se. ProvisioningAgent** e exclua eventos com a ID de evento "5", especificando o filtro "-5", conforme mostrado abaixo.

  ![Visualizador de Eventos do Windows](media/workday-inbound-tutorial/wd_event_viewer_01.png))

* Clique em **OK** e Classifique a exibição de resultado por coluna **de data e hora** .

### <a name="setting-up-azure-portal-audit-logs-for-service-troubleshooting"></a>Configurando portal do Azure logs de auditoria para solução de problemas de serviço

* Inicie o [portal do Azure](https://portal.azure.com)e navegue até a seção **logs de auditoria** do seu aplicativo de provisionamento do workday.
* Use o botão **colunas** na página logs de auditoria para exibir apenas as colunas a seguir na exibição (data, atividade, status, motivo do status). Essa configuração garante que você se concentre apenas nos dados que são relevantes para a solução de problemas.

  ![Colunas do log de auditoria](media/workday-inbound-tutorial/wd_audit_logs_00.png)

* Use os parâmetros de consulta de **destino** e **intervalo de datas** para filtrar a exibição. 
  * Defina o parâmetro de consulta de **destino** como a "ID do trabalhador" ou "ID do funcionário" do objeto de trabalho do workday.
  * Defina o **intervalo de datas** para um período de tempo apropriado durante o qual você deseja investigar se há erros ou problemas com o provisionamento.

  ![Filtros de log de auditoria](media/workday-inbound-tutorial/wd_audit_logs_01.png)

### <a name="understanding-logs-for-ad-user-account-create-operations"></a>Noções básicas sobre logs para operações de criação de conta de usuário do AD

Quando uma nova contratação no workday for detectada (digamos que com a ID do funcionário *21023*), o serviço de provisionamento do Azure ad tentará criar uma nova conta de usuário do AD para o trabalho e, no processo, criará quatro registros de log de auditoria, conforme descrito abaixo:

  [![criar Ops do log de auditoria](media/workday-inbound-tutorial/wd_audit_logs_02.png)](media/workday-inbound-tutorial/wd_audit_logs_02.png#lightbox)

Quando você clica em qualquer um dos registros de log de auditoria, a página de **detalhes da atividade** é aberta. Veja o que a página de **detalhes da atividade** exibe para cada tipo de registro de log.

* Registro de **importação do workday** : esse registro de log exibe as informações de trabalho buscadas do workday. Use as informações na seção *detalhes adicionais* do registro de log para solucionar problemas com a busca de dados do workday. Um registro de exemplo é mostrado abaixo, juntamente com ponteiros sobre como interpretar cada campo.

  ```JSON
  ErrorCode : None  // Use the error code captured here to troubleshoot Workday issues
  EventName : EntryImportAdd // For full sync, value is "EntryImportAdd" and for delta sync, value is "EntryImport"
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID (usually the Worker ID or Employee ID field)
  SourceAnchor : a071861412de4c2486eb10e5ae0834c3 // set to the WorkdayID (WID) associated with the record
  ```

* Registro de **importação do AD** : esse registro de log exibe informações da conta buscada do AD. Como durante a criação inicial do usuário não há nenhuma conta do AD, o *motivo do status da atividade* indicará que nenhuma conta com o valor do atributo de ID correspondente foi encontrada em Active Directory. Use as informações na seção *detalhes adicionais* do registro de log para solucionar problemas com a busca de dados do workday. Um registro de exemplo é mostrado abaixo, juntamente com ponteiros sobre como interpretar cada campo.

  ```JSON
  ErrorCode : None // Use the error code captured here to troubleshoot Workday issues
  EventName : EntryImportObjectNotFound // Implies that object was not found in AD
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID
  ```

  Para localizar os registros de log do agente de provisionamento correspondentes a essa operação de importação do AD, abra os logs do Windows Visualizador de Eventos e use **Localizar...** opção de menu para localizar entradas de log contendo o valor de atributo de propriedade ID correspondente/junção (neste caso, *21023*).

  ![Localizar](media/workday-inbound-tutorial/wd_event_viewer_02.png)

  Procure a entrada com *ID de evento = 9*, que fornecerá o filtro de pesquisa LDAP usado pelo agente para recuperar a conta do AD. Você pode verificar se esse é o filtro de pesquisa correto para recuperar entradas de usuário exclusivas.

  ![Pesquisa LDAP](media/workday-inbound-tutorial/wd_event_viewer_03.png)

  O registro que o segue imediatamente com a *ID do evento = 2* captura o resultado da operação de pesquisa e se retornou resultados.

  ![Resultados LDAP](media/workday-inbound-tutorial/wd_event_viewer_04.png)

* Registro de **ação da regra de sincronização** : esse registro de log exibe os resultados das regras de mapeamento de atributo e dos filtros de escopo configurados junto com a ação de provisionamento que será tomada para processar o evento de entrada do workday. Use as informações na seção *detalhes adicionais* do registro de log para solucionar problemas com a ação de sincronização. Um registro de exemplo é mostrado abaixo, juntamente com ponteiros sobre como interpretar cada campo.

  ```JSON
  ErrorCode : None // Use the error code captured here to troubleshoot sync issues
  EventName : EntrySynchronizationAdd // Implies that the object will be added
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID
  SourceAnchor : a071861412de4c2486eb10e5ae0834c3 // set to the WorkdayID (WID) associated with the profile in Workday
  ```

  Se houver problemas com as expressões de mapeamento de atributo ou se os dados de entrada do workday tiverem problemas (por exemplo: valor vazio ou nulo para os atributos necessários), você observará uma falha neste estágio com o ErrorCode fornecendo detalhes da falha.

* Registro de **exportação do AD** : esse registro de log exibe o resultado da operação de criação da conta do AD junto com os valores de atributo que foram definidos no processo. Use as informações na seção *detalhes adicionais* do registro de log para solucionar problemas com a operação de criação de conta. Um registro de exemplo é mostrado abaixo, juntamente com ponteiros sobre como interpretar cada campo. Na seção "detalhes adicionais", o "EventName" está definido como "EntryExportAdd", "Unindoproperty" é definido como o valor do atributo de ID correspondente, "SourceAnchor" é definido como WorkdayID (WID) associado ao registro e "TargetAnchor" é definido como o valor do atributo "objectGUID" do AD do usuário recém-criado. 

  ```JSON
  ErrorCode : None // Use the error code captured here to troubleshoot AD account creation issues
  EventName : EntryExportAdd // Implies that object will be created
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID
  SourceAnchor : a071861412de4c2486eb10e5ae0834c3 // set to the WorkdayID (WID) associated with the profile in Workday
  TargetAnchor : 83f0156c-3222-407e-939c-56677831d525 // set to the value of the AD "objectGuid" attribute of the new user
  ```

  Para localizar os registros de log do agente de provisionamento correspondentes a essa operação de exportação do AD, abra os logs do Windows Visualizador de Eventos e use o botão **Localizar...** opção de menu para localizar entradas de log contendo o valor de atributo de propriedade ID correspondente/junção (neste caso, *21023*).  

  Procure um registro HTTP POST correspondente ao carimbo de data/hora da operação de exportação com a *ID de evento = 2*. Esse registro conterá os valores de atributo enviados pelo serviço de provisionamento para o agente de provisionamento.

  [![adicionar SCIM](media/workday-inbound-tutorial/wd_event_viewer_05.png)](media/workday-inbound-tutorial/wd_event_viewer_05.png#lightbox)

  Imediatamente após o evento acima, deve haver outro evento que captura a resposta da operação criar conta do AD. Esse evento retorna o novo objectGUID criado no AD e é definido como o atributo TargetAnchor no serviço de provisionamento.

  [![adicionar SCIM](media/workday-inbound-tutorial/wd_event_viewer_06.png)](media/workday-inbound-tutorial/wd_event_viewer_06.png#lightbox)

### <a name="understanding-logs-for-manager-update-operations"></a>Noções básicas sobre logs para operações de atualização do Gerenciador

O atributo Manager é um atributo de referência no AD. O serviço de provisionamento não define o atributo Manager como parte da operação de criação do usuário. Em vez disso, o atributo Manager é definido como parte de uma operação de *atualização* depois que a conta do AD é criada para o usuário. Expandindo o exemplo acima, digamos que uma nova contratação com a ID de funcionário "21451" seja ativada no workday e o novo gerente de contratação (*21023*) já tenha uma conta do AD. Nesse cenário, a pesquisa dos logs de auditoria para o usuário 21451 mostra 5 entradas.

  [Atualização do ![Manager](media/workday-inbound-tutorial/wd_audit_logs_03.png)](media/workday-inbound-tutorial/wd_audit_logs_03.png#lightbox)

Os quatro primeiros registros são como aqueles que exploramos como parte da operação de criação do usuário. O quinto registro é a exportação associada à atualização de atributo do Gerenciador. O registro de log exibe o resultado da operação de atualização do Gerenciador de contas do AD, que é executada usando o atributo *objectGUID* do Gerenciador.

  ```JSON
  // Modified Properties
  Name : manager
  New Value : "83f0156c-3222-407e-939c-56677831d525" // objectGuid of the user 21023

  // Additional Details
  ErrorCode : None // Use the error code captured here to troubleshoot AD account creation issues
  EventName : EntryExportUpdate // Implies that object will be created
  JoiningProperty : 21451 // Value of the Workday attribute that serves as the Matching ID
  SourceAnchor : 9603bf594b9901693f307815bf21870a // WorkdayID of the user
  TargetAnchor : 43b668e7-1d73-401c-a00a-fed14d31a1a8 // objectGuid of the user 21451

  ```

### <a name="resolving-commonly-encountered-errors"></a>Resolvendo erros frequentemente encontrados

Esta seção aborda erros comumente vistos com o provisionamento de usuário do workday e como resolvê-lo. Os erros são agrupados da seguinte maneira:

* [Erros do agente de provisionamento](#provisioning-agent-errors)
* [Erros de conectividade](#connectivity-errors)
* [Erros de criação de conta de usuário do AD](#ad-user-account-creation-errors)
* [Erros de atualização da conta de usuário do AD](#ad-user-account-update-errors)

#### <a name="provisioning-agent-errors"></a>Erros do agente de provisionamento

|#|Cenário de erro |Causas prováveis|Resolução recomendada|
|--|---|---|---|
|1.| Erro ao instalar o agente de provisionamento com a mensagem de erro: o *serviço ' Microsoft Azure ad conectar o agente de provisionamento ' (AADConnectProvisioningAgent) falhou ao iniciar. Verifique se você tem privilégios suficientes para iniciar o sistema.* | Esse erro geralmente aparece se você estiver tentando instalar o agente de provisionamento em um controlador de domínio e a diretiva de grupo impedir que o serviço seja iniciado.  Ele também será visto se você tiver uma versão anterior do agente em execução e não tiver desinstalado antes de iniciar uma nova instalação.| Instale o agente de provisionamento em um servidor não DC. Verifique se as versões anteriores do agente foram desinstaladas antes de instalar o novo agente.|
|2.| O agente de provisionamento do serviço Windows ' Microsoft Azure AD Connect ' está em estado *inicial* e não muda para o estado de *execução* . | Como parte da instalação, o assistente de agente cria uma conta local (**NT Service\\AADConnectProvisioningAgent**) no servidor e essa é a conta de **logon** usada para iniciar o serviço. Se uma política de segurança no seu Windows Server impedir que contas locais executem os serviços, você encontrará esse erro. | Abra o *console de serviços*. Clique com o botão direito do mouse no serviço do Windows ' Microsoft Azure AD conectar agente de provisionamento ' e, na guia fazer logon, especifique a conta de um administrador de domínio para executar o serviço. Reinicie o serviço. |
|3.| Ao configurar o agente de provisionamento com seu domínio do AD na etapa *conectar Active Directory*, o assistente levará muito tempo tentando carregar o esquema do AD e eventualmente atingirá o tempo limite. | Geralmente, este erro aparece se o assistente não conseguir contactar o servidor de controlador de domínio do AD devido a problemas na firewall. | Na tela do assistente *conectar Active Directory* , ao fornecer as credenciais para o domínio do AD, há uma opção chamada *selecionar prioridade do controlador de domínio*. Use esta opção para selecionar um controlador de domínio que esteja no mesmo site que o servidor do agente e verifique se não há nenhuma regra de firewall bloqueando a comunicação. |

#### <a name="connectivity-errors"></a>Erros de conectividade

Se o serviço de provisionamento não puder se conectar ao workday ou Active Directory, isso poderá fazer com que o provisionamento entre em um estado em quarentena. Use a tabela abaixo para solucionar problemas de conectividade.

|#|Cenário de erro |Causas prováveis|Resolução recomendada|
|--|---|---|---|
|1.| Ao clicar em **testar conexão**, você receberá a mensagem de erro: *erro ao conectar a Active Directory. Verifique se o agente de provisionamento local está em execução e se está configurado com o domínio de Active Directory correto.* | Esse erro geralmente aparece se o agente de provisionamento não estiver em execução ou se houver um firewall bloqueando a comunicação entre o Azure AD e o agente de provisionamento. Você também poderá ver esse erro se o domínio não estiver configurado no assistente de agente. | Abra o console de *Serviços* no Windows Server para confirmar que o agente está em execução. Abra o assistente do agente de provisionamento e confirme se o domínio correto está registrado no agente.  |
|2.| O trabalho de provisionamento entra em estado de quarentena nos fins de semana (Sex-Sáb) e recebemos uma notificação por email de que há um erro com a sincronização. | Uma das causas comuns deste erro é o período de indisponibilidade planeado do Workday. Se estiver a utilizar um inquilino de implementação do Workday, tenha em conta que o Workday tem um período de indisponibilidade agendado para os inquilinos de implementação (normalmente, da noite de sexta-feira à manhã de sábado) e, durante esse período, as aplicações de aprovisionamento do Workday podem entrar em quarentena, pois não se conseguem ligar ao Workday. Regressam ao estado normal quando o inquilino de implementação do Workday estiver novamente online. Em casos raros, também poderá ver este erro se a palavra-passe de Utilizador de Sistema de Integração tiver sido alterada devido a uma atualização do inquilino ou se a conta estiver bloqueada ou expirada. | Verifique junto do seu administrador do Workday ou do seu parceiro de integração qual é o período de indisponibilidade do Workday, de modo a ignorar as mensagens de alerta durante esse período e confirmar a disponibilidade assim que a instância do Workday estiver de novo online.  |


#### <a name="ad-user-account-creation-errors"></a>Erros de criação de conta de usuário do AD

|#|Cenário de erro |Causas prováveis|Resolução recomendada|
|--|---|---|---|
|1.| Falha na operação de exportação no log de auditoria com o erro de mensagem *: OperationsError-SvcErr: ocorreu um erro de operação. Nenhuma referência superior foi configurada para o serviço de diretório. Portanto, o serviço de diretório não pode emitir referências a objetos fora desta floresta.* | Esse erro geralmente aparece se a UO do *contêiner de Active Directory* não estiver definida corretamente ou se houver problemas com o mapeamento de expressão usado para *parentDistinguishedName*. | Verifique o parâmetro da UO do *contêiner de Active Directory* para erros de digitação. Se estiver a utilizar *parentDistinguishedName* no mapeamento de atributos, confirme que é sempre avaliado para um contentor conhecido dentro do domínio do AD. Verifique o evento *Exportar* nos logs de auditoria para ver o valor gerado. |
|2.| Falha na operação de exportação no log de auditoria com o código de erro: *SystemForCrossDomainIdentityManagementBadResponse* e erro de mensagem *: ConstraintViolation-AtrErr: um valor na solicitação é inválido. Um valor para o atributo não estava no intervalo de valores aceitável. Detalhes da \Nerro: CONSTRAINT_ATT_TYPE-Company*. | Embora esse erro seja específico para o atributo *Company* , você pode ver esse erro para outros atributos como *CN* também. Esse erro aparece devido à restrição de esquema AD imposta. Por padrão, os atributos como *Company* e *CN* no AD têm um limite superior de 64 caracteres. Se o valor proveniente do workday tiver mais de 64 caracteres, você verá essa mensagem de erro. | Verifique o evento *Exportar* nos logs de auditoria para ver o valor do atributo relatado na mensagem de erro. Considere truncar o valor proveniente do workday usando a função [mid](../manage-apps/functions-for-customizing-application-data.md#mid) ou alterar os mapeamentos para um atributo do AD que não tenha restrições de comprimento semelhantes.  |

#### <a name="ad-user-account-update-errors"></a>Erros de atualização da conta de usuário do AD

Durante o processo de atualização da conta de usuário do AD, o serviço de provisionamento lê as informações do workday e do AD, executa as regras de mapeamento de atributos e determina se qualquer alteração precisa entrar em vigor. Portanto, um evento Update é disparado. Se qualquer uma dessas etapas encontrar uma falha, ela será registrada nos logs de auditoria. Use a tabela a seguir para solucionar erros comuns de atualização.

|#|Cenário de erro |Causas prováveis|Resolução recomendada|
|--|---|---|---|
|1.| Falhas de ação de regra de sincronização no log de auditoria com a mensagem *EventName = EntrySynchronizationError e ErrorCode = EndpointUnavailable*. | Esse erro aparecerá se o serviço de provisionamento não puder recuperar dados de perfil de usuário do Active Directory devido a um erro de processamento encontrado pelo agente de provisionamento local. | Verifique o agente de provisionamento Visualizador de Eventos logs para eventos de erro que indicam problemas com a operação de leitura (filtrar por ID do evento #2). |
|2.| O atributo Manager no AD não é atualizado para determinados usuários no AD. | A causa mais provável desse erro é se você estiver usando regras de escopo e o gerente do usuário não fizer parte do escopo. Você também poderá encontrar esse problema se o atributo de ID correspondente do Gerenciador (por exemplo, EmployeeID) não for encontrado no domínio do AD de destino ou não estiver definido como o valor correto. | Examine o filtro de escopo e adicione o usuário do Gerenciador no escopo. Verifique o perfil do gerente no AD para certificar-se de que há um valor para o atributo de ID correspondente. |

## <a name="managing-your-configuration"></a>Gerenciando sua configuração

Esta seção descreve como você pode estender, personalizar e gerenciar ainda mais sua configuração de provisionamento de usuário controlada pelo workday. Ele aborda os seguintes tópicos:

* [Personalizando a lista de atributos de usuário do workday](#customizing-the-list-of-workday-user-attributes)  
* [Exportando e importando sua configuração](#exporting-and-importing-your-configuration)

### <a name="customizing-the-list-of-workday-user-attributes"></a>Personalizando a lista de atributos de usuário do workday

Os aplicativos de provisionamento do WORKDAY para Active Directory e o Azure AD incluem uma lista padrão de atributos de usuário do workday que você pode selecionar. No entanto, essas listas não são abrangentes. O workday dá suporte a muitas centenas de atributos de usuário possíveis, que podem ser padrão ou exclusivos para seu locatário do workday.

O serviço de provisionamento do Azure AD dá suporte à capacidade de personalizar sua lista ou atributo WORKDAY para incluir todos os atributos expostos na operação de [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) da API de recursos humanos.

Para fazer essa alteração, você deve usar o [workday Studio](https://community.workday.com/studio-download) para extrair as expressões XPath que representam os atributos que você deseja usar e, em seguida, adicioná-las à sua configuração de provisionamento usando o editor de atributos avançado no portal do Azure.

**Para recuperar uma expressão XPath para um atributo de usuário do workday:**

1. Baixe e instale o [workday Studio](https://community.workday.com/studio-download). Você precisará de uma conta da Comunidade do WORKDAY para acessar o instalador.

2. Baixe o arquivo WSDL Human_Resources do workday desta URL: https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Human_Resources.wsdl

3. Inicie o workday Studio.

4. Na barra de comandos, selecione a opção **Workday > testar o serviço Web no testador** .

5. Selecione **externo**e selecione o arquivo WSDL Human_Resources que você baixou na etapa 2.

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio1.png)

6. Defina o campo **local** como `https://IMPL-CC.workday.com/ccx/service/TENANT/Human_Resources`, mas substituindo "impl-CC" por seu tipo de instância real e "locatário" pelo nome de seu locatário real.

7. Definir **operação** como **Get_Workers**

8.  Clique no pequeno link **Configurar** abaixo dos painéis solicitação/resposta para definir suas credenciais de workday. Verifique a **autenticação**e insira o nome de usuário e a senha de sua conta do sistema de integração do workday. Certifique-se de Formatar o nome de usuário como nome\@locatário e deixe a opção **WS-Security UserNameToken** selecionada.

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio2.png)

9. Selecione **OK**.

10. No painel de **solicitação** , Cole o XML abaixo e defina **EMPLOYEE_ID** como a ID de funcionário de um usuário real em seu locatário do workday. Selecione um usuário que tenha o atributo populado que você deseja extrair.

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <env:Envelope xmlns:env="http://schemas.xmlsoap.org/soap/envelope/" xmlns:xsd="https://www.w3.org/2001/XMLSchema">
      <env:Body>
        <wd:Get_Workers_Request xmlns:wd="urn:com.workday/bsvc" wd:version="v21.1">
          <wd:Request_References wd:Skip_Non_Existing_Instances="true">
            <wd:Worker_Reference>
              <wd:ID wd:type="Employee_ID">21008</wd:ID>
            </wd:Worker_Reference>
          </wd:Request_References>
          <wd:Response_Group>
            <wd:Include_Reference>true</wd:Include_Reference>
            <wd:Include_Personal_Information>true</wd:Include_Personal_Information>
            <wd:Include_Employment_Information>true</wd:Include_Employment_Information>
            <wd:Include_Management_Chain_Data>true</wd:Include_Management_Chain_Data>
            <wd:Include_Organizations>true</wd:Include_Organizations>
            <wd:Include_Reference>true</wd:Include_Reference>
            <wd:Include_Transaction_Log_Data>true</wd:Include_Transaction_Log_Data>
            <wd:Include_Photo>true</wd:Include_Photo>
            <wd:Include_User_Account>true</wd:Include_User_Account>
          <wd:Include_Roles>true</wd:Include_Roles>
          </wd:Response_Group>
        </wd:Get_Workers_Request>
      </env:Body>
    </env:Envelope>
    ```

11. Clique na **solicitação enviar** (seta verde) para executar o comando. Se for bem-sucedida, a resposta deverá aparecer no painel de **resposta** . Verifique a resposta para garantir que ela tenha os dados da ID de usuário inserida e não um erro.

12. Se for bem-sucedido, copie o XML do painel de **resposta** e salve-o como um arquivo XML.

13. Na barra de comandos do workday Studio, selecione **arquivo > abrir arquivo...** e abra o arquivo XML que você salvou. Essa ação abrirá o arquivo no editor de XML do workday Studio.

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio3.png)

14. Na árvore de arquivos, navegue pelo **/env: Envelope > env: corpo > WD: Get_Workers_Response > WD: Response_Data > WD: Worker** para localizar os dados do usuário.

15. Em **WD: Worker**, localize o atributo que você deseja adicionar e selecione-o.

16. Copie a expressão XPath do atributo selecionado para fora do campo **caminho do documento** .

17. Remova o **/env: envelope/env: Body/WD: Get_Workers_Response/WD: Response_Data/** prefixo da expressão copiada.

18. Se o último item na expressão copiada for um nó (exemplo: "/WD: Birth_Date"), acrescente **/Text ()** no final da expressão. Isso não será necessário se o último item for um atributo (por exemplo: "/@wd: tipo").

19. O resultado deve ser algo como `wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()`. Esse valor é o que você vai copiar para o portal do Azure.

**Para adicionar seu atributo personalizado de usuário do workday à sua configuração de provisionamento:**

1. Inicie o [portal do Azure](https://portal.azure.com)e navegue até a seção provisionamento do seu aplicativo de provisionamento do workday, conforme descrito anteriormente neste tutorial.

2. Defina o **status de provisionamento** como **desativado**e selecione **salvar**. Esta etapa ajudará a garantir que as alterações entrarão em vigor somente quando você estiver pronto.

3. Em **mapeamentos**, selecione **sincronizar trabalhos do WORKDAY para o local Active Directory** (ou **sincronizar trabalhos do workday com o Azure ad**).

4. Role até a parte inferior da próxima tela e selecione **Mostrar opções avançadas**.

5. Selecione **Editar lista de atributos para workday**.

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio_aad1.png)

6. Role até a parte inferior da lista de atributos para onde estão os campos de entrada.

7. Para **nome**, insira um nome de exibição para seu atributo.

8. Para **tipo**, selecione o tipo que corresponde adequadamente ao seu atributo (a**cadeia de caracteres** é mais comum).

9. Para **expressão de API**, insira a expressão XPath que você copiou do workday Studio. Exemplo: `wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()`

10. Selecione **Adicionar atributo**.

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio_aad2.png)

11. Selecione **salvar** acima e, em seguida, **Sim** para a caixa de diálogo. Feche a tela de mapeamento de atributo se ela ainda estiver aberta.

12. De volta à guia **provisionamento** principal, selecione **sincronizar trabalhos do WORKDAY para o local Active Directory** (ou **sincronizar trabalhadores com o Azure ad**) novamente.

13. Selecione **Adicionar novo mapeamento**.

14. O novo atributo agora deve aparecer na lista **atributo de origem** .

15. Adicione um mapeamento para o novo atributo conforme desejado.

16. Quando terminar, lembre-se de definir o **status de provisionamento** de volta para **ativado** e salvar.

### <a name="exporting-and-importing-your-configuration"></a>Exportar e importar configuração

Consulte o artigo [exportando e importando a configuração de provisionamento](../manage-apps/export-import-provisioning-configuration.md)

## <a name="managing-personal-data"></a>Gerir dados pessoais

A solução de provisionamento do WORKDAY para Active Directory requer que um agente de provisionamento seja instalado em um servidor Windows local, e esse agente cria logs no log de eventos do Windows que podem conter dados pessoais, dependendo do seu atributo do WORKDAY para o AD mapeamentos. Para estar em conformidade com as obrigações de privacidade do usuário, você pode garantir que nenhum dado seja retido nos logs de eventos além de 48 horas, configurando uma tarefa agendada do Windows para limpar o log de eventos.

O serviço de provisionamento do Azure AD se enquadra na categoria **processador de dados** da classificação GDPR. Como um pipeline de processador de dados, o serviço fornece serviços de processamento de dados para parceiros-chave e consumidores finais. O serviço de provisionamento do Azure AD não gera dados de usuário e não tem controle independente sobre quais dados pessoais são coletados e como eles são usados. A recuperação de dados, a agregação, a análise e os relatórios no serviço de provisionamento do Azure AD são baseados em dados corporativos existentes.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-hybrid-note.md)]

Em relação à retenção de dados, o serviço de provisionamento do Azure AD não gera relatórios, executa análises ou fornece informações além de 30 dias. Portanto, o serviço de provisionamento do Azure AD não armazena, processa nem retém dados além de 30 dias. Esse design é compatível com as normas de GDPR, os regulamentos de conformidade de privacidade da Microsoft e as políticas de retenção de dados do Azure AD.

## <a name="next-steps"></a>Passos seguintes

* [Saiba como examinar os logs e obter relatórios sobre a atividade de provisionamento](../manage-apps/check-status-user-account-provisioning.md)
* [Saiba como configurar o logon único entre o workday e o Azure Active Directory](workday-tutorial.md)
* [Saiba como integrar outros aplicativos SaaS com o Azure Active Directory](tutorial-list.md)
* [Saiba como usar Microsoft Graph APIs para gerenciar as configurações de provisionamento](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)
