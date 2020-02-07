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
ms.openlocfilehash: a7e5dc9c177dbddda8bf229ec7949f53b70e616c
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77064311"
---
# <a name="tutorial-configure-workday-for-automatic-user-provisioning"></a>Tutorial: configurar o WORKDAY para provisionamento automático de usuário

O objetivo deste tutorial é mostrar as etapas que você precisa executar para importar perfis de trabalho do WORKDAY para Active Directory e Azure Active Directory, com write-back opcional de endereço de email e nome de usuário para workday.

## <a name="overview"></a>Descrição geral

O serviço de prestação de [utilizadores do Azure Ative Directory](../app-provisioning/user-provisioning.md) integra-se com a API de Recursos Humanos do Dia do [Trabalho,](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) a fim de fornecer contas de utilizador. O Azure AD usa essa conexão para habilitar os seguintes fluxos de trabalho de provisionamento de usuário:

* **Provisioning users to Ative Directory** - Provision selected sets of users from Workday to one or more Ative Directory domains.

* **Disponibilizando utilizadores apenas na nuvem ao Diretório Ativo Do Azure** - Em cenários em que não é utilizado o Ative Directory no local, os utilizadores podem ser aprovisionados diretamente do Workday para o Azure Ative Directory utilizando o serviço de provisionamento de utilizadores da AD Azure.

* **Escreva de volta o endereço de e-mail e o nome** de utilizador para o Workday - O serviço de fornecimento de utilizadores da AD Azure pode escrever os endereços de e-mail e o nome de utilizador de Azure AD de volta ao Workday.

### <a name="what-human-resources-scenarios-does-it-cover"></a>Quais cenários de recursos humanos ele cobre?

Os fluxos de trabalho de provisionamento de usuário do workday com suporte do serviço de provisionamento de usuário do Azure AD habilitam a automação dos seguintes cenários de gerenciamento de ciclo de vida de identidade e recursos humanos:

* **Contratação** de novos colaboradores - Quando um novo colaborador é adicionado ao Workday, uma conta de utilizador é automaticamente criada em Ative Directory, Azure Ative Directory, e opcionalmente Office 365 e [outras aplicações SaaS suportadas pela Azure AD,](../app-provisioning/user-provisioning.md)com redação do endereço de e-mail para o Workday.

* **Atributo sipróprio e atualizações** de perfil - Quando um registo de empregados é atualizado no Workday (como o seu nome, título ou gestor), a sua conta de utilizador será automaticamente atualizada em Diretório Ativo, Diretório Ativo Azure e opcionalmente Office 365 e [outras aplicações SaaS suportadas pela Azure AD](../app-provisioning/user-provisioning.md).

* **Rescisões** de funcionários - Quando um trabalhador é despedido no Workday, a sua conta de utilizador é automaticamente desativada em Diretório Ativo, Diretório Ativo Azure e opcionalmente Office 365 e [outras aplicações SaaS suportadas pela Azure AD.](../app-provisioning/user-provisioning.md)

* **Recontratações** de empregados - Quando um empregado é recontratado no Workday, a sua conta antiga pode ser automaticamente reativada ou represtada (dependendo da sua preferência) ao Ative Directory, Ao Diretório Ativo azure e opcionalmente ao Office 365 e [a outras aplicações saaS apoiadas pela Azure AD](../app-provisioning/user-provisioning.md).

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>A qual esta solução de provisionamento de usuário mais adequada?

Essa solução de provisionamento de usuário do workday é ideal para:

* Organizações que desejam uma solução predefinida baseada em nuvem para provisionamento de usuário do workday

* Organizações que exigem o provisionamento direto de usuários do WORKDAY para Active Directory ou Azure Active Directory

* Organizações que exigem que os utilizadores sejam aprovisionados utilizando dados obtidos a partir do módulo HCM do dia de trabalho (ver [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html))

* Organizações que exigem a adesão, mudança e deixando os utilizadores sincronizados com uma ou mais Florestas de Diretórios Ativos, Domínios e OUs com base apenas em informações de mudança detetadas no módulo HCM do Dia de Trabalho (ver [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html))

* Organizações que usam o Office 365 para email

## <a name="solution-architecture"></a>Arquitetura de Soluções

Esta seção descreve a arquitetura da solução de provisionamento de usuário de ponta a ponta para ambientes híbridos comuns. Há dois fluxos relacionados:

* Fluxo de **dados de RH autoritário – do Dia de Trabalho ao Diretório Ativo no local:** Neste fluxo de eventos de trabalhadores (tais como Novas Contratações, Transferências, Rescisões) ocorrem primeiro na nuvem Workday HR inquilino e, em seguida, os dados do evento fluem para o diretório ativo no local através da Azure AD e do Agente de Provisionamento. Dependendo do evento, ele pode levar a criar/atualizar/habilitar/desabilitar operações no AD.
* **Email e Username Writeback Flow – do Diretório Ativo no local para o Dia** do Trabalho: Uma vez que a criação da conta esteja completa no Ative Directory, é sincronizada com azure AD Através do Azure AD Connect e o e-mail e o atributo de nome de utilizador podem ser reescritos ao Workday.

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

Antes de começar sua integração do workday, verifique os pré-requisitos abaixo e leia as orientações a seguir sobre como corresponder a sua arquitetura de Active Directory atual e os requisitos de provisionamento de usuário com as soluções fornecidas pelo Azure Active Directory. Um plano de [implementação](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-plans) abrangente com folhas de cálculo de planeamento também está disponível para ajudá-lo a colaborar com o seu parceiro de integração Workday e partes interessadas em RH.

Esta seção aborda os seguintes aspectos do planejamento:

* [Pré-requisitos](#prerequisites)
* [Selecionando aplicações de conector de provisionamento para implementar](#selecting-provisioning-connector-apps-to-deploy)
* [Planejamento da implantação do Agente de Provisionamento de Ligação AD Azure](#planning-deployment-of-azure-ad-connect-provisioning-agent)
* [Integração com vários domínios de Diretório Ativo](#integrating-with-multiple-active-directory-domains)
* [Planejamento do dia de trabalho para o utilizador de diretório ativo atribui mapeamento e transformações](#planning-workday-to-active-directory-user-attribute-mapping-and-transformations)

### <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Uma licença de assinatura Azure AD Premium P1 ou superior válida para cada usuário que será originada do workday e provisionada no Active Directory local ou Azure Active Directory.
* Acesso de administrador global do Azure AD para configurar o agente de provisionamento
* Um locatário de implementação do WORKDAY para fins de teste e integração
* Permissões de administrador no WORKDAY para criar um usuário de integração do sistema e fazer alterações para testar dados de funcionários para fins de teste
* Para o fornecimento de utilizadores ao Ative Directory, é necessário um servidor que execute o Windows Server 2012 ou superior com o tempo de funcionamento .NET 4.7.1+ para acolher o agente de [provisionamento no local.](https://go.microsoft.com/fwlink/?linkid=847801)
* [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) para sincronizar utilizadores entre Diretório Ativo e Azure AD

### <a name="selecting-provisioning-connector-apps-to-deploy"></a>Selecionando os aplicativos do conector de provisionamento a serem implantados

Para facilitar o provisionamento de fluxos de trabalho entre o workday e o Active Directory, o Azure AD fornece vários aplicativos de conector de provisionamento que você pode adicionar da Galeria de aplicativos do Azure AD:

![Galeria de Aplicativo Azure AD](./media/workday-inbound-tutorial/wd_gallery.png)

* **Workday to Ative Directory User Provisioning** - Esta aplicação facilita o fornecimento de conta de utilizador do Workday para um único domínio de Diretório Ativo. Se você tiver vários domínios, poderá adicionar uma instância desse aplicativo da Galeria de aplicativos do Azure AD para cada domínio de Active Directory ao qual você precisa provisionar.

* **Workday to Azure AD User Provisioning** - Embora o Azure AD Connect seja a ferramenta que deve ser usada para sincronizar utilizadores de Diretório Ativo para o Diretório Ativo do Azure, esta aplicação pode ser usada para facilitar o fornecimento de utilizadores apenas na nuvem desde o Dia de Trabalho a um único inquilino do Diretório Ativo Azure.

* **Workday Writeback** - Esta aplicação facilita a redação dos endereços de e-mail do utilizador do Azure Ative Directory para o Workday.

> [!TIP]
> O aplicativo "workday" regular é usado para configurar o logon único entre o workday e o Azure Active Directory.

Use o gráfico de fluxo de decisão abaixo para identificar quais aplicativos de provisionamento do workday são relevantes para seu cenário.
    ![Fluxograma de Decisão](./media/workday-inbound-tutorial/wday_app_flowchart.png "Fluxograma de decisão")

Use o Sumário para ir para a seção relevante deste tutorial.

### <a name="planning-deployment-of-azure-ad-connect-provisioning-agent"></a>Planejando a implantação do agente de provisionamento do Azure AD Connect

> [!NOTE]
> Esta seção é relevante apenas se você planeja implantar o WORKDAY para Active Directory aplicativo de provisionamento de usuário. Se você estiver implantando o aplicativo de provisionamento de usuários do Azure AD, você poderá ignorar isso.

O WORKDAY para a solução de provisionamento de usuário do AD requer a implantação de um ou mais agentes de provisionamento em servidores que executam o Windows 2012 R2 ou superior com o mínimo de 4 GB de RAM e do tempo de execução do .NET 4.7.1 +. As considerações a seguir devem ser levadas em conta antes da instalação do agente de provisionamento:

* Verifique se o servidor host que executa o agente de provisionamento tem acesso à rede para o domínio do AD de destino
* O assistente de configuração do agente de provisionamento registra o agente com seu locatário do Azure AD e o processo de registro requer acesso a *. msappproxy.net pela porta SSL 443. Verifique se as regras de firewall de saída estão em vigor para habilitar essa comunicação. O agente suporta a configuração de [procuração HTTPS de saída](#how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication).
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
| Não. do WORKDAY para os aplicativos de provisionamento de usuário do AD a serem configurados no portal do Azure | Um aplicativo por domínio filho |

  ![Cenário 2](./media/workday-inbound-tutorial/dep_scenario2.png)

#### <a name="deployment-scenario-3--single-workday-tenant---disjoint-ad-forests"></a>Cenário de implantação #3: único locatário do workday-> florestas do AD não contíguos

Esse cenário envolve o provisionamento de usuários do WORKDAY para domínios em florestas do AD não contíguos. Aqui está a configuração de produção recomendada para essa implantação.

|   |   |
| - | - |
| Não. de provisionamento de agentes para implantação local | 3 por floresta de AD não contíguo |
| Não. do WORKDAY para os aplicativos de provisionamento de usuário do AD a serem configurados no portal do Azure | Um aplicativo por domínio filho |

  ![Cenário 3](./media/workday-inbound-tutorial/dep_scenario3.png)

### <a name="planning-workday-to-active-directory-user-attribute-mapping-and-transformations"></a>Planejando o WORKDAY para Active Directory mapeamento e transformações de atributos de usuário

> [!NOTE]
> Esta seção é relevante apenas se você planeja implantar o WORKDAY para Active Directory aplicativo de provisionamento de usuário. Se você estiver implantando o aplicativo de provisionamento de usuários do Azure AD, você poderá ignorar isso.

Antes de configurar o provisionamento de usuário para um domínio Active Directory, considere as perguntas a seguir. As respostas a essas perguntas determinarão como os filtros de escopo e os mapeamentos de atributo precisam ser definidos.

* **Que utilizadores no Workday precisam de ser aprovisionados a esta floresta de Diretório Ativo?**

  * *Exemplo: Utilizadores onde o atributo "Empresa" do Dia de Trabalho contém o valor "Contoso", e o atributo "Worker_Type" contém "Regular"*

* **Como é que os utilizadores são encaminhados para diferentes unidades de organização (OUs)?**

  * *Exemplo: Os utilizadores são encaminhados para OUs que correspondem a uma localização de escritório, conforme definido nos atributos "Município" e "Country_Region_Reference"*

* **Como devem ser preenchidos os seguintes atributos no Diretório Ativo?**

  * Nome comum (CN)
    * *Exemplo: Utilize o valor User_ID do Dia de Trabalho, conforme definido pelos recursos humanos*

  * ID do funcionário (employeeId)
    * *Exemplo: Use o valor Worker_ID do Dia de Trabalho*

  * Nome da conta SAM (sAMAccountName)
    * *Exemplo: Utilize o valor do dia de trabalho User_ID, filtrado através de uma expressão de fornecimento de anúncios Azure para remover caracteres ilegais*

  * Nome principal do usuário (userPrincipalName)
    * *Exemplo: Utilize o valor User_ID do Dia de Trabalho, com uma expressão de fornecimento de Anúncios Azure para anexar um nome de domínio*

* **Como devem os utilizadores ser acompanhados entre o Workday e o Ative Directy?**

  * *Exemplo: Os utilizadores com um valor "Worker_ID" específico do Workday são combinados com utilizadores de Diretório Ativo onde o "employeeID" tem o mesmo valor. Se o valor Worker_ID não for encontrado no Diretório Ativo, crie um novo utilizador.*
  
* **A floresta de Diretório Ativo já contém as IDs do utilizador necessárias para que a lógica correspondente funcione?**

  * *Exemplo: Se esta configuração for uma nova implementação do Dia do Trabalho, recomenda-se que o Diretório Ativo seja pré-povoado com os valores Worker_ID do Dia de Trabalho corretos (ou valor único de id de eleição) para manter a lógica correspondente o mais simples possível.*

Como configurar e configurar esses aplicativos de conector de provisionamento especial é o assunto das seções restantes deste tutorial. Os aplicativos que você escolher configurar dependerão de quais sistemas você precisa provisionar e quantos domínios Active Directory e locatários do AD do Azure estão em seu ambiente.

## <a name="configure-integration-system-user-in-workday"></a>Configurar o usuário do sistema de integração no workday

Um requisito comum de todos os conectores de provisionamento do workday é que eles exigem credenciais de um usuário do sistema de integração do WORKDAY para se conectar à API de recursos humanos do workday. Esta seção descreve como criar um usuário do sistema de integração no workday e tem as seguintes seções:

* [Criação de um utilizador do sistema de integração](#creating-an-integration-system-user)
* [Criação de um grupo de segurança de integração](#creating-an-integration-security-group)
* [Configurar permissões de política de segurança de domínio](#configuring-domain-security-policy-permissions)
* [Configurar permissões de política de segurança de processos empresariais](#configuring-business-process-security-policy-permissions)
* [Ativar mudanças na política de segurança](#activating-security-policy-changes)

> [!NOTE]
> É possível ignorar esse procedimento e, em vez disso, usar uma conta de administrador global do workday como a conta de integração do sistema. Isso pode funcionar bem para demonstrações, mas não é recomendado para implantações de produção.

### <a name="creating-an-integration-system-user"></a>Criando um usuário do sistema de integração

**Para criar um utilizador do sistema de integração:**

1. Entre no seu locatário do workday usando uma conta de administrador. Na **Aplicação Workday,** introduza o utilizador na caixa de pesquisa e, em seguida, clique em Criar utilizador do Sistema de **Integração**.

   ![Criar o utilizador](./media/workday-inbound-tutorial/wd_isu_01.png "Criar utilizador")
2. Complete a tarefa do Utilizador do **Sistema de Integração Criar** fornecendo um nome de utilizador e uma palavra-passe para um novo Utilizador do Sistema de Integração.  
  
   * Deixe a **nova palavra-passe requerida na** opção Seguinte Sinal in sem verificação, porque este utilizador irá iniciar sessão programáticamente.
   * Deixe o **Timeout Minutes** da Sessão com o seu valor padrão de 0, o que impedirá que as sessões do utilizador se cronometrem prematuramente.
   * Selecione a opção Não permitir que as **UI Sessions,** uma vez que fornece uma camada adicional de segurança que impede um utilizador de aceder à palavra-passe do sistema de integração para iniciar sessão no Workday.

   ![Criar o Utilizador do Sistema de Integração](./media/workday-inbound-tutorial/wd_isu_02.png "Criar usuário do sistema de integração")

### <a name="creating-an-integration-security-group"></a>Criando um grupo de segurança de integração

Nesta etapa, você criará um grupo de segurança de sistema de integração irrestrito ou restrito no workday e atribuirá o usuário do sistema de integração criado na etapa anterior a esse grupo.

**Para criar um grupo de segurança:**

1. Introduza criar um grupo de segurança na caixa de pesquisa e, em seguida, clique em **Criar Grupo de Segurança**.

    ![Grupo CreateSecurity](./media/workday-inbound-tutorial/wd_isu_03.png "Grupo createsecurity")
2. Complete a tarefa do **Grupo Criar Segurança.** 

   * Há dois tipos de grupos de segurança no workday:
     * **Sem restrições:** Todos os membros do grupo de segurança podem aceder a todas as instâncias de dados protegidas pelo grupo de segurança.
     * **Constrangido:** Todos os membros do grupo de segurança têm acesso contextual a um subconjunto de instâncias de dados (linhas) a que o grupo de segurança pode aceder.
   * Consulte seu parceiro de integração do WORKDAY para selecionar o tipo de grupo de segurança apropriado para a integração.
   * Assim que conhecer o tipo de grupo, selecione **Integration System Security Group (Unconstrained)** ou Integration System Security Group **(Constranged)** do Tipo de Dropdown do **Grupo de Segurança Inquilino.**

     ![Grupo CreateSecurity](./media/workday-inbound-tutorial/wd_isu_04.png "Grupo createsecurity")

3. Depois que a criação do grupo de segurança for bem-sucedida, você verá uma página em que você pode atribuir Membros ao grupo de segurança. Adicione o novo usuário do sistema de integração criado na etapa anterior a esse grupo de segurança. Se estiver a utilizar um grupo de segurança *constrangido,* também terá de selecionar o âmbito de organização apropriado.

    ![Editar Grupo de Segurança](./media/workday-inbound-tutorial/wd_isu_05.png "Editar grupo de segurança")

### <a name="configuring-domain-security-policy-permissions"></a>Configurando permissões de política de segurança de domínio

Nesta etapa, você concederá permissões de política de "segurança de domínio" para os dados de trabalho para o grupo de segurança.

**Para configurar as permissões da política de segurança do domínio:**

1. Introduza a **configuração** de segurança do domínio na caixa de pesquisa e, em seguida, clique no relatório de configuração de **configuração**de domínio do link .  

    ![Políticas de Segurança do Domínio](./media/workday-inbound-tutorial/wd_isu_06.png "Políticas de segurança de domínio")  
2. Na caixa de texto **domínio,** procure os seguintes domínios e adicione-os ao filtro um a um.  
   * *Provisionamento de Conta Externa*
   * *Dados dos trabalhadores: Relatórios dos trabalhadores públicos*
   * *Dados da Pessoa: Informações de contato de trabalho*
   * *Dados do Trabalhador: Todas as Posições*
   * *Dados dos Trabalhadores: Informação atual sobre pessoal*
   * *Dados do Trabalhador: Título de negócio no perfil do trabalhador*
   * *Contas do dia de trabalho*
   
     ![Políticas de Segurança do Domínio](./media/workday-inbound-tutorial/wd_isu_07.png "Políticas de segurança de domínio")  

     ![Políticas de Segurança do Domínio](./media/workday-inbound-tutorial/wd_isu_08.png "Políticas de segurança de domínio") 

     Clique em **OK**.

3. No relatório que aparece, selecione a elipse (...) que aparece ao lado do Fornecimento de **Conta Externa** e clique na opção menu **Domínio -> Editar Permissões** de Política de Segurança

    ![Políticas de Segurança do Domínio](./media/workday-inbound-tutorial/wd_isu_09.png "Políticas de segurança de domínio")  

4. Na página de Permissões de Política de Segurança do **Domínio de Edição,** desloque-se até à secção **Permissões**de Integração . Clique no sinal "+" para adicionar o grupo de sistema de integração à lista de grupos de segurança com permissões de integração **Get** e **Put.**

    ![Editar Permissão](./media/workday-inbound-tutorial/wd_isu_10.png "Editar permissão")  

5. Clique no sinal "+" para adicionar o grupo de sistema de integração à lista de grupos de segurança com permissões de integração **Get** e **Put.**

    ![Editar Permissão](./media/workday-inbound-tutorial/wd_isu_11.png "Editar permissão")  

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

**Para configurar permissões de política de segurança de processos empresariais:**

1. Introduza a Política de **Processo de Negócios** na caixa de pesquisa e clique no link Editar a tarefa de Política de Segurança do **Processo de Negócios.**  

    ![Políticas de segurança do processo de negócio](./media/workday-inbound-tutorial/wd_isu_12.png "Políticas de segurança do processo comercial")  

2. Na caixa de texto **do Tipo de Processo de Negócios,** procure *contacto* e selecione processo de negócio **de Alteração de Contactos** e clique em **OK**.

    ![Políticas de segurança do processo de negócio](./media/workday-inbound-tutorial/wd_isu_13.png "Políticas de segurança do processo comercial")  

3. Na página política de segurança do processo de **negócio de edição,** percorra a secção Manter Informações de **Contacto (Serviço Web).**

    ![Políticas de segurança do processo de negócio](./media/workday-inbound-tutorial/wd_isu_14.png "Políticas de segurança do processo comercial")  

4. Selecione e adicione o novo grupo de segurança do sistema de integração à lista de grupos de segurança que podem iniciar a solicitação de serviços Web. Clique em **Done**. 

    ![Políticas de segurança do processo de negócio](./media/workday-inbound-tutorial/wd_isu_15.png "Políticas de segurança do processo comercial")  

### <a name="activating-security-policy-changes"></a>Ativando alterações de política de segurança

**Para ativar alterações de política de segurança:**

1. Introduza ativar na caixa de pesquisa e, em seguida, clique no link **Ativar alterações**de política de segurança pendente .

    ![Ativar](./media/workday-inbound-tutorial/wd_isu_16.png "Activate")

1. Inicie a tarefa de ativação da política de segurança pendente, inserindo um comentário para fins de auditoria e, em seguida, clique em **OK**.
1. Complete a tarefa no ecrã seguinte, verificando a caixa de verificação **Confirme,** e, em seguida, clique **em OK**.

    ![Ativar a Segurança Pendente](./media/workday-inbound-tutorial/wd_isu_18.png "Ativar segurança pendente")  

## <a name="configuring-user-provisioning-from-workday-to-active-directory"></a>Configurando o provisionamento de usuário do WORKDAY para o Active Directory

Esta seção fornece etapas para o provisionamento de contas de usuário do WORKDAY para cada domínio Active Directory dentro do escopo da sua integração.

* [Adicione a aplicação de conector de provisionamento e descarregue o Agente de Provisionamento](#part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent)
* [Instalar e configurar no local agente de provisionamento(s)](#part-2-install-and-configure-on-premises-provisioning-agents)
* [Configure a conectividade com o Workday e o Ative Directy](#part-3-in-the-provisioning-app-configure-connectivity-to-workday-and-active-directory)
* [Configure mapeamentos de atributos](#part-4-configure-attribute-mappings)
* [Ativar e lançar o fornecimento de utilizadores](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent"></a>Parte 1: Adicionar o aplicativo do conector de provisionamento e baixar o agente de provisionamento

**Para configurar o Workday para o fornecimento de Diretório Ativo:**

1. Aceda a <https://portal.azure.com>.

2. No portal Azure, procure e selecione **Azure Ative Directory**.

3. Selecione **Aplicações Empresariais,** em **seguida, todas as aplicações**.

4. Selecione **Adicionar uma aplicação**e selecione a categoria **All.**

5. Procure o **Workday Provisioning para Ative Directory**e adicione essa aplicação a partir da galeria.

6. Depois da adição da aplicação e do ecrã de detalhes da aplicação, selecione **Provisioning**.

7. Mude o **modo** **de provisionamento** para **automático**.

8. Clique na faixa de informações exibida para baixar o agente de provisionamento. 

   ![Agente de descarregamento](./media/workday-inbound-tutorial/pa-download-agent.png "Tela de download do agente")


### <a name="part-2-install-and-configure-on-premises-provisioning-agents"></a>Parte 2: instalar e configurar agente (s) de provisionamento local

Para provisionar para Active Directory local, o agente de provisionamento deve ser instalado em um servidor que tenha o .NET 4.7.1 + estrutura e acesso de rede aos domínios de Active Directory desejados.

> [!TIP]
> Pode verificar a versão da estrutura .NET no seu servidor utilizando as instruções [aqui](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed)fornecidas .
> Se o servidor não tiver .NET 4.7.1 ou mais instalado, pode descarregá-lo a partir [daqui](https://support.microsoft.com/help/4033342/the-net-framework-4-7-1-offline-installer-for-windows).  

Transfira o instalador do agente baixado para o host do servidor e siga as etapas fornecidas abaixo para concluir a configuração do agente.

1. Entre no Windows Server em que você deseja instalar o novo agente.

1. Lance o instalador do Agente de Provisionamento, concorde com os termos e clique no botão **Instalar.**

   ![Instalar ecrã](./media/workday-inbound-tutorial/pa_install_screen_1.png "Tela de instalação")
   
1. Após a instalação estar concluída, o assistente será lançado e verá o ecrã **AD Connect Azure.** Clique no botão **Authenticate** para ligar à sua instância Azure AD.

   ![Ligar AD Azure](./media/workday-inbound-tutorial/pa_install_screen_2.png "Ligar ao Azure AD")
   
1. Autentique em sua instância do Azure AD usando credenciais de administrador global.

   ![Administrador Auth](./media/workday-inbound-tutorial/pa_install_screen_3.png "Autenticação de administrador")

   > [!NOTE]
   > As credenciais de administrador do Azure AD são usadas somente para se conectar ao seu locatário do Azure AD. O agente não armazena as credenciais localmente no servidor.

1. Após autenticação bem sucedida com a AD Azure, verá o ecrã connect **Ative Directy.** Neste passo, introduza o nome de domínio AD e clique no botão **Adicionar Diretório.**

   ![Adicionar Diretório](./media/workday-inbound-tutorial/pa_install_screen_4.png "Adicionar diretório")
  
1. Agora, você será solicitado a inserir as credenciais necessárias para se conectar ao domínio do AD. No mesmo ecrã, pode utilizar a prioridade do controlador de **domínio Select** para especificar os controladores de domínio que o agente deve utilizar para enviar pedidos de fornecimento.

   ![Credenciais de domínio](./media/workday-inbound-tutorial/pa_install_screen_5.png)
   
1. Depois de configurar o domínio, o instalador exibirá uma lista de domínios configurados. Neste ecrã, pode repetir passo #5 e #6 para adicionar mais domínios ou clicar em **Next** para proceder ao registo do agente.

   ![Domínios configurados](./media/workday-inbound-tutorial/pa_install_screen_6.png "Domínios configurados")

   > [!NOTE]
   > Se você tiver vários domínios do AD (por exemplo, na.contoso.com, emea.contoso.com), adicione cada domínio individualmente à lista.
   > Somente a adição do domínio pai (por exemplo, contoso.com) não é suficiente. Você deve registrar cada domínio filho com o agente.
   
1. Reveja os detalhes da configuração e clique em **Confirmar** para registar o agente.
  
   ![Confirmar tela](./media/workday-inbound-tutorial/pa_install_screen_7.png "Confirmar tela")
   
1. O assistente de configuração exibe o progresso do registro do agente.
  
   ![Registo do Agente](./media/workday-inbound-tutorial/pa_install_screen_8.png "Registro do agente")
   
1. Assim que o registo do agente for bem sucedido, pode clicar na **Saída** para sair do Assistente.
  
   ![Ecrã de saída](./media/workday-inbound-tutorial/pa_install_screen_9.png "Sair da tela")
   
1. Verifique a instalação do agente e verifique se ele está em execução abrindo o snap-in "serviços" e procure o serviço chamado "agente de provisionamento do Microsoft Azure AD Connect"
  
   ![Serviços](./media/workday-inbound-tutorial/services.png)

### <a name="part-3-in-the-provisioning-app-configure-connectivity-to-workday-and-active-directory"></a>Parte 3: no aplicativo de provisionamento, configure a conectividade com o workday e o Active Directory
Nesta etapa, estabelecemos a conectividade com o workday e o Active Directory no portal do Azure. 

1. No portal Azure, volte ao Workday para ative Directory User Provisioning App criado na [Parte 1](#part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent)

1. Complete a secção **de Credenciais de Administrador** da seguinte forma:

   * **Nome** de utilizador do Administrador – Introduza o nome de utilizador da conta do sistema de integração Workday, com o nome de domínio do arrendatário anexado. Deve parecer algo como: **nome de utilizador\@tenant_name**

   * **Senha de administração –** Insira a palavra-passe da conta do sistema de integração do Dia de Trabalho

   * **URL do inquilino –** Insira o URL no ponto final dos serviços web workday para o seu inquilino. Este valor deve parecer: https://wd3-impl-services1.workday.com/ccx/service/contoso4, onde o *contoso4* é substituído pelo seu nome de inquilino correto e *wd3-impl* é substituído pela corda ambiental correta.

   * Floresta de **Diretório Ativo -** O "Nome" do seu domínio de Diretório Ativo, registado no agente. Use a lista suspensa para selecionar o domínio de destino para provisionamento. Este valor é tipicamente uma corda como: *contoso.com*

   * **Recipiente de Diretório Ativo -** Introduza o DN do recipiente onde o agente deve criar contas de utilizador por padrão.
        Exemplo: *OU=Utilizadores Standard,OU=Utilizadores,DC=contoso,DC=teste*
        
     > [!NOTE]
     > Esta definição só entra em jogo para criações de conta de utilizador se o atributo *parentDistinguishedName* não estiver configurado nos mapeamentos do atributo. Essa configuração não é usada para operações de pesquisa ou atualização de usuário. A subárvore de domínio inteira cai no escopo da operação de pesquisa.

   * **Email de notificação –** Insira o seu endereço de e-mail e verifique a caixa de verificação "enviar e-mail se ocorrer falha".

     > [!NOTE]
     > O Serviço de Provisionamento de AD Azure envia notificação por e-mail se o trabalho de provisionamento entrar em estado de [quarentena.](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)

   * Clique no botão **de ligação** de teste. Se o teste de ligação for bem sucedido, clique no botão **Guardar** na parte superior. Se ele falhar, verifique se as credenciais do workday e as credenciais do AD configuradas na instalação do agente são válidas.

     ![Portal do Azure](./media/workday-inbound-tutorial/wd_1.png)

   * Uma vez que as credenciais são guardadas com sucesso, a secção **de Mapeamentos** apresentará o mapeamento padrão **Synchronize Workday Workers to On Premises Ative Directory**

### <a name="part-4-configure-attribute-mappings"></a>Parte 4: configurar mapeamentos de atributo

Nesta seção, você configurará como os dados do usuário fluem do WORKDAY para o Active Directory.

1. No separador provisionamento em **Mapeamentos,** clique em **Sincronizar trabalhadores do dia**de trabalho para o diretório ativo no local .

1. No campo **Source Object Scope,** pode selecionar quais os conjuntos de utilizadores no Workday que devem estar disponíveis para o fornecimento de AD, definindo um conjunto de filtros baseados em atributos. O escopo padrão é "todos os usuários no workday". Filtros de exemplo:

   * Exemplo: escopo para usuários com IDs de trabalho entre 1 milhão e 2 milhões (exceto 2 milhões)

      * Atributo: Workerid

      * Operador: correspondência de REGEX

      * Valor: (1[0-9][0-9][0-9][0-9][0-9][0-9])

   * Exemplo: somente funcionários e não usuários contingentes

      * Atributo: EmployeeID

      * Operador: não é nulo

   > [!TIP]
   > Quando você estiver configurando o aplicativo de provisionamento pela primeira vez, será necessário testar e verificar os mapeamentos e as expressões de atributo para garantir que ele esteja dando o resultado desejado. A Microsoft recomenda a utilização dos filtros de deteção no âmbito do Âmbito do **Objeto Fonte** para testar os seus mapeamentos com alguns utilizadores de teste do Workday. Depois de verificar se os mapeamentos funcionam, você pode remover o filtro ou expandi-lo gradualmente para incluir mais usuários.

   > [!CAUTION] 
   > O comportamento padrão do mecanismo de provisionamento é desabilitar/excluir usuários que saem do escopo. Isso pode não ser desejável em sua integração do seu workday com o AD. Para anular este comportamento padrão, consulte o artigo [Sem a eliminação das contas dos utilizadores que saem do âmbito](../app-provisioning/skip-out-of-scope-deletions.md)
  
1. No campo **Target Object Actions,** pode filtrar globalmente quais as ações que são realizadas no Diretório Ativo. **Criar** e **Atualizar** são mais comuns.

1. Na secção de **mapeamento sacar,** pode definir como cada dia de trabalho atribui o mapa aos atributos do Diretório Ativo.

1. Clique num mapeamento de atributoexistente para atualizá-lo, ou clique **Em adicionar novo mapeamento** na parte inferior do ecrã para adicionar novos mapeamentos. Um mapeamento de atributo individual oferece suporte a essas propriedades:

      * **Tipo de mapeamento**

         * **Direto** – Escreve o valor do atributo do Dia de Trabalho ao atributo AD, sem alterações

         * **Constante** - Escreva um valor de corda estático e constante para o atributo AD

         * **Expressão** – Permite-lhe escrever um valor personalizado para o atributo AD, com base num ou mais atributos do Dia de Trabalho. [Para mais informações, consulte este artigo sobre expressões.](../app-provisioning/functions-for-customizing-application-data.md)

      * **Atributo de origem** - O atributo do utilizador do Dia de Trabalho. Se o atributo que procura não estiver presente, consulte personalizar [a lista de atributos do utilizador do Dia](#customizing-the-list-of-workday-user-attributes)de Trabalho .

      * **Valor predefinido** – Opcional. Se o atributo de origem tiver um valor vazio, o mapeamento gravará esse valor em vez disso.
            A configuração mais comum é deixar em branco.

      * **Atributo-alvo** – O atributo do utilizador no Diretório Ativo.

      * **Combine objetos usando este atributo** – Se este mapeamento deve ou não ser usado para identificar exclusivamente os utilizadores entre o Workday e o Ative Diretório. Esse valor é normalmente definido no campo ID de trabalho para workday, que normalmente é mapeado para um dos atributos de ID de funcionário no Active Directory.

      * **Precedência correspondente** – Podem ser definidos múltiplos atributos correspondentes. Quando há vários, eles são avaliados na ordem definida por esse campo. Assim que uma correspondência for encontrada, nenhum atributo correspondente será avaliado.

      * **Aplique este mapeamento**

         * **Sempre** – Aplique este mapeamento tanto na criação do utilizador como nas ações de atualização

         * **Apenas durante** a criação - Aplicar este mapeamento apenas em ações de criação de utilizadores

1. Para guardar os seus mapeamentos, clique em **Guardar** no topo da secção Attribute-Mapping.

   ![Portal do Azure](./media/workday-inbound-tutorial/wd_2.png)

#### <a name="below-are-some-example-attribute-mappings-between-workday-and-active-directory-with-some-common-expressions"></a>Abaixo estão alguns exemplos de mapeamentos de atributo entre workday e Active Directory, com algumas expressões comuns

* A expressão que mapeia para o atributo *parentDistinguishedName* é usada para fornecer um utilizador a diferentes OUs com base em um ou mais atributos de origem do Dia do Trabalho. Este exemplo aqui coloca os usuários em UOs diferentes com base em qual cidade eles estão.

* O atributo *do userPrincipalName* no Ative Directory é gerado utilizando a função de desduplicação [SelectUniqueValue](../app-provisioning/functions-for-customizing-application-data.md#selectuniquevalue) que verifica a existência de um valor gerado no domínio ad-alvo e apenas o define se for único.  

* [Há documentação sobre a escrita de expressões aqui.](../app-provisioning/functions-for-customizing-application-data.md) Esta seção inclui exemplos de como remover caracteres especiais.

| ATRIBUTO WORKDAY | ATRIBUTO DO ACTIVE DIRECTORY |  ID DE CORRESPONDÊNCIA? | CRIAR/ATUALIZAR |
| ---------- | ---------- | ---------- | ---------- |
| **TrabalhadorID**  |  Id de empregado | **Sim** | Gravado em criar somente |
| **Nome preferidoData**    |  CN    |   |   Gravado em criar somente |
| **SelectUniqueValue("\@join", join(".", \[Primeiro Nome\], \[LastName\], "contoso.com"), Join("\@", Join (".", Mid, mid(\[primeiro nome\], 1, 1), \[LastName\], "contoso.com"), Join("\@", "\[Nome do Primeiro Nome\], 1, 2), \[LastName\], "contoso.com")**   | userPrincipalName     |     | Gravado em criar somente 
| **Substitua(\[\]de utilizador, "\[\\\\/ \\\\\\ \\\]\\\\\\\\\\\[     \\\\:\\;\\\\\\=\\\|   \\ \\, \\\\\\+   \\  \\de \\\*?\\\\&lt;\\ \\&gt;\])", "1, 20"), "\\\\.]\*\$(\\file:///.) *$)", , "", , )**      |    sAMAccountName            |     |         Gravado em criar somente |
| **Switch,\[Ative\], "0", "True", "1", "False")** |  accountDisabled      |     | Criar + atualizar |
| **Primeiro nome**   | givenName       |     |    Criar + atualizar |
| **Apelido**   |   sn   |     |  Criar + atualizar |
| **Nome preferidoData**  |  displayName |     |   Criar + atualizar |
| **Empresa**         | Empresa   |     |  Criar + atualizar |
| **Organização de Supervisão**  | Departamento  |     |  Criar + atualizar |
| **GestorReferência**   | Manager  |     |  Criar + atualizar |
| **BusinessTitle**   |  Título     |     |  Criar + atualizar | 
| **Data de Endereçoline**    |  streetAddress  |     |   Criar + atualizar |
| **Município**   |   l   |     | Criar + atualizar |
| **PaísReferênciaTwoLetter**      |   Co |     |   Criar + atualizar |
| **PaísReferênciaTwoLetter**    |  c  |     |         Criar + atualizar |
| **Referência countryregion** |  St     |     | Criar + atualizar |
| **WorkSpaceReference** | physicalDeliveryOfficeName    |     |  Criar + atualizar |
| **Código Postal**  |   postalCode  |     | Criar + atualizar |
| **PrimaryWorkPhone**  |  telephoneNumber   |     | Criar + atualizar |
| **Fax**      | facsimileTelephoneNumber     |     |    Criar + atualizar |
| **Móvel**  |    móvel       |     |       Criar + atualizar |
| **Referência local** |  preferredLanguage  |     |  Criar + atualizar |                                               
| **Switch(\[Município\], "OU=Utilizadores Standard,OU=Utilizadores,OU=Default,OU=Localizações,DC=contoso,DC=com", "Dallas", "OU=Utilizadores Standard,OU=Utilizadores,OU=Utilizadores,OU=Dallas,OU=Localizações,DC=com", "Austin", "OU=Utilizadores Standard,OU=Utilizadores,OU=Austin,OU=Locations,DC=contoso,DC=com, "Seattle", "OU=Utilizadores Standard,OU=Utilizadores,OU=Utilizadores,OU=Seattle,OU=Locations,DC=contoso,DC=com", "Londres", "OU=Utilizadores Standard,OU=Utilizadores,OU=Londres,OU=Locations,DC=contoso,DC=com")**  | parentDistinguishedName     |     |  Criar + atualizar |

Uma vez concluída a configuração de mapeamento do atributo, pode agora [ativar e lançar o serviço](#enable-and-launch-user-provisioning)de fornecimento do utilizador .

## <a name="configuring-user-provisioning-to-azure-ad"></a>Configurando o provisionamento de usuário para o Azure AD

As seções a seguir descrevem as etapas para configurar o provisionamento de usuário do WORKDAY para o Azure AD para implantações somente em nuvem.

* [Adicionar a aplicação de conector Azure AD e criar a ligação ao Dia do Trabalho](#part-1-adding-the-azure-ad-provisioning-connector-app-and-creating-the-connection-to-workday)
* [Configure Workday e Azure AD atribuem mapeamentos](#part-2-configure-workday-and-azure-ad-attribute-mappings)
* [Ativar e lançar o fornecimento de utilizadores](#enable-and-launch-user-provisioning)

> [!IMPORTANT]
> Siga o procedimento abaixo se você tiver usuários somente de nuvem que precisam ser provisionados no Azure AD e não no local Active Directory.

### <a name="part-1-adding-the-azure-ad-provisioning-connector-app-and-creating-the-connection-to-workday"></a>Parte 1: adicionando o aplicativo do conector de provisionamento do Azure AD e criando a conexão com o workday

**Para configurar o fornecimento de Workday para Azure Ative Directory para utilizadores apenas na nuvem:**

1. Aceda a <https://portal.azure.com>.

2. No portal Azure, procure e selecione **Azure Ative Directory**.

3. Selecione **Aplicações Empresariais,** em **seguida, todas as aplicações**.

4. Selecione **Adicionar uma aplicação**e, em seguida, selecione a categoria **All.**

5. Procure o **workday para o fornecimento de Anúncios Azure,** e adicione essa aplicação a partir da galeria.

6. Depois da adição da aplicação e do ecrã de detalhes da aplicação, selecione **Provisioning**.

7. Mude o **modo** **de provisionamento** para **automático**.

8. Complete a secção **de Credenciais de Administrador** da seguinte forma:

   * **Nome** de utilizador do Administrador – Introduza o nome de utilizador da conta do sistema de integração Workday, com o nome de domínio do arrendatário anexado. Deve parecer algo como: username@contoso4

   * **Senha de administração –** Insira a palavra-passe da conta do sistema de integração do Dia de Trabalho

   * **URL do inquilino –** Insira o URL no ponto final dos serviços web workday para o seu inquilino. Este valor deve parecer: https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources, onde o *contoso4* é substituído pelo seu nome de inquilino correto e *wd3-impl* é substituído pela corda ambiental correta. Se essa URL não for conhecida, trabalhe com o parceiro de integração do workday ou com o representante de suporte para determinar a URL correta a ser usada.

   * **Email de notificação –** Insira o seu endereço de e-mail e verifique a caixa de verificação "enviar e-mail se ocorrer falha".

   * Clique no botão **de ligação** de teste.

   * Se o teste de ligação for bem sucedido, clique no botão **Guardar** na parte superior. Se falhar, verifique se a URL e as credenciais do workday são válidas no workday.

### <a name="part-2-configure-workday-and-azure-ad-attribute-mappings"></a>Parte 2: configurar o workday e os mapeamentos de atributo do Azure AD

Nesta seção, você configurará como os dados do usuário fluem do WORKDAY para Azure Active Directory para usuários somente na nuvem.

1. No separador provisionamento em **Mapeamentos,** clique em **Sincronizar trabalhadores para Azure AD**.

2. No campo **Source Object Scope,** pode selecionar quais os conjuntos de utilizadores no Workday que devem estar disponíveis para o fornecimento ao Azure AD, definindo um conjunto de filtros baseados em atributos. O escopo padrão é "todos os usuários no workday". Filtros de exemplo:

   * Exemplo: escopo para usuários com IDs de trabalho entre 1 milhão e 2 milhões

      * Atributo: Workerid

      * Operador: correspondência de REGEX

      * Valor: (1[0-9][0-9][0-9][0-9][0-9][0-9]]]]

   * Exemplo: somente trabalhadores contingentes e não funcionários regulares

      * Atributo: Contingentid

      * Operador: não é nulo

3. No campo **Target Object Actions,** pode filtrar globalmente quais as ações que são realizadas em Azure AD. **Criar** e **Atualizar** são mais comuns.

4. Na secção de **mapeamento sacar,** pode definir como cada dia de trabalho atribui o mapa aos atributos do Diretório Ativo.

5. Clique num mapeamento de atributoexistente para atualizá-lo, ou clique **Em adicionar novo mapeamento** na parte inferior do ecrã para adicionar novos mapeamentos. Um mapeamento de atributo individual oferece suporte a essas propriedades:

   * **Tipo de mapeamento**

      * **Direto** – Escreve o valor do atributo do Dia de Trabalho ao atributo AD, sem alterações

      * **Constante** - Escreva um valor de corda estático e constante para o atributo AD

      * **Expressão** – Permite-lhe escrever um valor personalizado para o atributo AD, com base num ou mais atributos do Dia de Trabalho. [Para mais informações, consulte este artigo sobre expressões.](../app-provisioning/functions-for-customizing-application-data.md)

   * **Atributo de origem** - O atributo do utilizador do Dia de Trabalho. Se o atributo que procura não estiver presente, consulte personalizar [a lista de atributos do utilizador do Dia](#customizing-the-list-of-workday-user-attributes)de Trabalho .

   * **Valor predefinido** – Opcional. Se o atributo de origem tiver um valor vazio, o mapeamento gravará esse valor em vez disso.
            A configuração mais comum é deixar em branco.

   * **Atributo-alvo** – O atributo do utilizador em Azure AD.

   * **Combine objetos usando este atributo** – Se este atributo deve ou não ser usado para identificar exclusivamente os utilizadores entre o Workday e o Azure AD. Esse valor é normalmente definido no campo ID de trabalho para workday, que é normalmente mapeado para o atributo de ID de funcionário (novo) ou um atributo de extensão no Azure AD.

   * **Precedência correspondente** – Podem ser definidos múltiplos atributos correspondentes. Quando há vários, eles são avaliados na ordem definida por esse campo. Assim que uma correspondência for encontrada, nenhum atributo correspondente será avaliado.

   * **Aplique este mapeamento**

     * **Sempre** – Aplique este mapeamento tanto na criação do utilizador como nas ações de atualização

     * **Apenas durante** a criação - Aplicar este mapeamento apenas em ações de criação de utilizadores

6. Para guardar os seus mapeamentos, clique em **Guardar** no topo da secção Attribute-Mapping.

Uma vez concluída a configuração de mapeamento do atributo, pode agora [ativar e lançar o serviço](#enable-and-launch-user-provisioning)de fornecimento do utilizador .

## <a name="configuring-azure-ad-attribute-writeback-to-workday"></a>Configurando o Write-back de atributo do Azure AD para workday

Siga estas instruções para configurar o Write-back de endereços de email do usuário e nome de usuário de Azure Active Directory para workday.

* [Adicionar a aplicação de conector Writeback e criar a ligação ao Dia do Trabalho](#part-1-adding-the-writeback-connector-app-and-creating-the-connection-to-workday)
* [Configure mapeamento sinuoso de atributos](#part-2-configure-writeback-attribute-mappings)
* [Ativar e lançar o fornecimento de utilizadores](#enable-and-launch-user-provisioning)

### <a name="part-1-adding-the-writeback-connector-app-and-creating-the-connection-to-workday"></a>Parte 1: adicionando o aplicativo do conector de write-back e criando a conexão com o workday

**Para configurar o conector Workday Writeback:**

1. Aceda a <https://portal.azure.com>.

2. No portal Azure, procure e selecione **Azure Ative Directory**.

3. Selecione **Aplicações Empresariais,** em **seguida, todas as aplicações**.

4. **Selecione Adicionar uma aplicação**e, em seguida, selecione a categoria **All.**

5. Procure **workday Writeback**e adicione essa aplicação na galeria.

6. Depois da adição da aplicação e do ecrã de detalhes da aplicação, selecione **Provisioning**.

7. Mude o **modo** **de provisionamento** para **automático**.

8. Complete a secção **de Credenciais de Administrador** da seguinte forma:

   * **Nome** de utilizador do Administrador – Introduza o nome de utilizador da conta do sistema de integração Workday, com o nome de domínio do arrendatário anexado. Deve parecer algo como: *nome de utilizador\@contoso4*

   * **Senha de administração –** Insira a palavra-passe da conta do sistema de integração do Dia de Trabalho

   * **URL do inquilino –** Insira o URL no ponto final dos serviços web workday para o seu inquilino. Este valor deve parecer: https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources, onde o *contoso4* é substituído pelo seu nome de inquilino correto e *wd3-impl* é substituído pela corda ambiental correta (se necessário).

   * **Email de notificação –** Insira o seu endereço de e-mail e verifique a caixa de verificação "enviar e-mail se ocorrer falha".

   * Clique no botão **de ligação** de teste. Se o teste de ligação for bem sucedido, clique no botão **Guardar** na parte superior. Se falhar, verifique se a URL e as credenciais do workday são válidas no workday.

### <a name="part-2-configure-writeback-attribute-mappings"></a>Parte 2: configurar mapeamentos de atributo de write-back

Nesta seção, você configurará como os atributos de write-back fluem do Azure AD para o workday. No momento, o conector só dá suporte a Write-back de endereço de email e nome de usuário para workday.

1. No separador provisionamento em **Mapeamentos,** clique em **Synchronize Azure Ative Directory Users to Workday**.

2. No campo **Source Object Scope,** pode filtrar opcionalmente, que conjuntos de utilizadores no Diretório Ativo do Azure devem ter os seus endereços de e-mail reescritos no Workday. O escopo padrão é "todos os usuários no Azure AD".

3. Na secção de **mapeamento** sacar, atualize o ID correspondente para indicar o atributo no Diretório Ativo Azure onde o ID do trabalhador do Workday ou identificação do empregado é armazenado. Um método de correspondência popular é sincronizar a ID de funcionário ou a ID do trabalhador do workday com o extensionAttribute1-15 no Azure AD e, em seguida, usar esse atributo no Azure AD para corresponder os usuários de volta no workday.

4. Normalmente, mapeia o atributo do utilizador Azure AD *PrincipalName* ao atributo do *UserID* do Dia de Trabalho e mapeie o atributo de *correio* AD Azure ao atributo do Workday *EmailAddress.* Para guardar os seus mapeamentos, clique em **Guardar** no topo da secção Attribute-Mapping.

Uma vez concluída a configuração de mapeamento do atributo, pode agora [ativar e lançar o serviço](#enable-and-launch-user-provisioning)de fornecimento do utilizador .

## <a name="enable-and-launch-user-provisioning"></a>Habilitar e iniciar o provisionamento de usuário

Depois que as configurações do aplicativo de provisionamento do workday tiverem sido concluídas, você poderá ativar o serviço de provisionamento no portal do Azure.

> [!TIP]
> Por padrão, quando você ativa o serviço de provisionamento, ele inicia as operações de provisionamento para todos os usuários no escopo. Se houver erros no mapeamento ou nos problemas de dados do workday, o trabalho de provisionamento poderá falhar e entrar no estado de quarentena. Para evitar isto, como uma boa prática, recomendamos configurar o filtro **Source Object Scope** e testar os seus mapeamentos de atributos com alguns utilizadores de teste antes de lançar a sincronização completa para todos os utilizadores. Depois de verificar se os mapeamentos funcionam e estão fornecendo os resultados desejados, você pode remover o filtro ou expandi-lo gradualmente para incluir mais usuários.

1. No separador **provisionamento,** coloque o estado de **provisionamento** **ligado**.

2. Clique em **Guardar**.

3. Esta operação iniciará a sincronização inicial, o que pode levar um número variável de horas, dependendo de quantos usuários estiverem no locatário do workday. 

4. A qualquer momento, verifique o separador de **registos de auditoria** no portal Azure para ver que ações o serviço de provisionamento realizou. Os logs de auditoria listam todos os eventos de sincronização individuais executados pelo serviço de provisionamento, como quais usuários estão sendo lidos do workday e, posteriormente, adicionados ou atualizados para Active Directory. Consulte a seção de solução de problemas para obter instruções sobre como examinar os logs de auditoria e corrigir os erros de provisionamento.

5. Uma vez concluída a sincronização inicial, escreverá um relatório resumo de auditoria no separador **Provisioning,** como mostrado abaixo.

   ![Portal do Azure](./media/workday-inbound-tutorial/wd_3.png)

## <a name="frequently-asked-questions-faq"></a>Perguntas Mais Frequentes (FAQ)

* **Questões de capacidade de solução**
  * [Ao processar uma nova contratação a partir do Workday, como é que a solução define a palavra-passe para a nova conta de utilizador no Diretório Ativo?](#when-processing-a-new-hire-from-workday-how-does-the-solution-set-the-password-for-the-new-user-account-in-active-directory)
  * [A solução suporta o envio de notificações por e-mail após a conclusão das operações de provisionamento?](#does-the-solution-support-sending-email-notifications-after-provisioning-operations-complete)
  * [Como posso gerir a entrega de senhas para novas contratações e fornecer de forma segura um mecanismo para redefinir a sua palavra-passe?](#how-do-i-manage-delivery-of-passwords-for-new-hires-and-securely-provide-a-mechanism-to-reset-their-password)
  * [A solução cache Perfis de utilizador do Dia de Trabalho na nuvem ad ad azure ou na camada do agente de provisionamento?](#does-the-solution-cache-workday-user-profiles-in-the-azure-ad-cloud-or-at-the-provisioning-agent-layer)
  * [A solução suporta a atribuição de grupos aD no local ao utilizador?](#does-the-solution-support-assigning-on-premises-ad-groups-to-the-user)
  * [Que APIs workday utiliza a solução para consultar e atualizar perfis de trabalhadores do Workday?](#which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles)
  * [Posso configurar o meu inquilino HCM do Workday com dois inquilinos da AD Azure?](#can-i-configure-my-workday-hcm-tenant-with-two-azure-ad-tenants)
  * [Por que razão a aplicação de fornecimento de utilizadores "Workday to Azure AD" não é suportada se implementámos o Azure AD Connect?](#why-workday-to-azure-ad-user-provisioning-app-is-not-supported-if-we-have-deployed-azure-ad-connect)
  * [Como posso sugerir melhorias ou solicitar novas funcionalidades relacionadas com a integração da AD Workday e Azure?](#how-do-i-suggest-improvements-or-request-new-features-related-to-workday-and-azure-ad-integration)

* **Perguntas do Agente de Provisionamento**
  * [Qual é a versão GA do Agente de Provisionamento?](#what-is-the-ga-version-of-the-provisioning-agent)
  * [Como conheço a versão do meu Agente de Provisionamento?](#how-do-i-know-the-version-of-my-provisioning-agent)
  * [A Microsoft pressiona automaticamente as atualizações do Agente de Provisionamento?](#does-microsoft-automatically-push-provisioning-agent-updates)
  * [Posso instalar o Agente de Provisionamento no mesmo servidor que executa o Azure AD Connect?](#can-i-install-the-provisioning-agent-on-the-same-server-running-azure-ad-connect)
  * [Como configurar o Agente de Provisionamento para utilizar um servidor proxy para comunicação HTTP de saída?](#how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication)
  * [Como posso garantir que o Agente de Provisionamento é capaz de comunicar com o inquilino da AD Azure e que não há firewalls bloqueando portas exigidas pelo agente?](#how-do-i-ensure-that-the-provisioning-agent-is-able-to-communicate-with-the-azure-ad-tenant-and-no-firewalls-are-blocking-ports-required-by-the-agent)
  * [Como posso desregistar o domínio associado ao meu Agente de Provisionamento?](#how-do-i-de-register-the-domain-associated-with-my-provisioning-agent)
  * [Como desinstalar o Agente de Provisionamento?](#how-do-i-uninstall-the-provisioning-agent)
  
* **Dia de trabalho a atributo sacar questões de mapeamento e configuração**
  * [Como posso fazer o apoio ou exportar uma cópia de trabalho do meu Workday Provisioning Attribute Mapping e Schema?](#how-do-i-back-up-or-export-a-working-copy-of-my-workday-provisioning-attribute-mapping-and-schema)
  * [Tenho atributos personalizados no Workday e no Ative Directy. Como configurar a solução para trabalhar com os meus atributos personalizados?](#i-have-custom-attributes-in-workday-and-active-directory-how-do-i-configure-the-solution-to-work-with-my-custom-attributes)
  * [Posso fornecer a foto do utilizador do Workday ao Ative Directory?](#can-i-provision-users-photo-from-workday-to-active-directory)
  * [Como posso sincronizar os números de telemóvel do Workday com base no consentimento do utilizador para uso público?](#how-do-i-sync-mobile-numbers-from-workday-based-on-user-consent-for-public-usage)
  * [Como posso verpuleto nomes em AD com base nos atributos do departamento/país/cidade do utilizador e lidar com variações regionais?](#how-do-i-format-display-names-in-ad-based-on-the-users-departmentcountrycity-attributes-and-handle-regional-variances)
  * [Como posso usar selectUniqueValue para gerar valores únicos para atributo samAccountName?](#how-can-i-use-selectuniquevalue-to-generate-unique-values-for-samaccountname-attribute)
  * [Como posso remover caracteres com diacritics e convertê-los em alfabetos ingleses normais?](#how-do-i-remove-characters-with-diacritics-and-convert-them-into-normal-english-alphabets)

### <a name="solution-capability-questions"></a>Perguntas de capacidade da solução

#### <a name="when-processing-a-new-hire-from-workday-how-does-the-solution-set-the-password-for-the-new-user-account-in-active-directory"></a>Ao processar uma nova contratação do workday, como a solução define a senha da nova conta de usuário no Active Directory?

Quando o agente de provisionamento local recebe uma solicitação para criar uma nova conta do AD, ele gera automaticamente uma senha aleatória complexa criada para atender aos requisitos de complexidade de senha definidos pelo servidor do AD e define isso no objeto de usuário. Essa senha não é registrada em nenhum lugar.

#### <a name="does-the-solution-support-sending-email-notifications-after-provisioning-operations-complete"></a>A solução dá suporte ao envio de notificações por email após a conclusão das operações de provisionamento?

Não, o envio de notificações por email após a conclusão das operações de provisionamento não tem suporte na versão atual.

#### <a name="how-do-i-manage-delivery-of-passwords-for-new-hires-and-securely-provide-a-mechanism-to-reset-their-password"></a>Como fazer gerenciar a entrega de senhas para novas contratações e fornecer com segurança um mecanismo para redefinir sua senha?

Uma das etapas finais envolvidas no novo provisionamento de conta do AD é a entrega da senha temporária atribuída à conta do AD do usuário. Muitas empresas ainda usam a abordagem tradicional de fornecer a senha temporária para o gerente do usuário, que, em seguida, passa a senha para o novo trabalho de contratação/contingente. Esse processo tem uma falha de segurança inerente e há uma opção disponível para implementar uma abordagem melhor usando os recursos do Azure AD.

Como parte do processo de contratação, as equipes de RH geralmente executam uma verificação em segundo plano e examinaremos o número de celular da nova contratação. Com o WORKDAY para integração de provisionamento de usuário do AD, você pode criar sobre esse fato e distribuir um recurso de redefinição de senha de autoatendimento para o usuário no dia 1. Isso é feito propagando o atributo "número do celular" da nova contratação do WORKDAY para o AD e, em seguida, do AD para o Azure AD usando o Azure AD Connect. Uma vez que o "Número Móvel" esteja presente no Azure AD, pode ativar o Reset de [Palavra-Passe self-service (SSPR)](../authentication/howto-sspr-authenticationdata.md) para a conta do utilizador, de modo a que, no dia 1, um novo aluguer possa utilizar o número de telemóvel registado e verificado para autenticação.

#### <a name="does-the-solution-cache-workday-user-profiles-in-the-azure-ad-cloud-or-at-the-provisioning-agent-layer"></a>A solução armazena em cache os perfis de usuário do workday na nuvem do Azure AD ou na camada do agente de provisionamento?

Não, a solução não mantém um cache de perfis de usuário. O serviço de provisionamento do Azure AD simplesmente atua como um processador de dados, lendo dados do workday e gravando no Active Directory de destino ou no Azure AD. Consulte a secção Gestão de [dados pessoais](#managing-personal-data) para obter detalhes relacionados com a privacidade do utilizador e a retenção de dados.

#### <a name="does-the-solution-support-assigning-on-premises-ad-groups-to-the-user"></a>A solução dá suporte à atribuição de grupos do AD locais para o usuário?

Não há suporte para essa funcionalidade no momento. A solução alternativa recomendada é implantar um script do PowerShell que consulta o ponto de extremidade de API do Graph do Azure AD para obter dados de log de auditoria e usá-lo para disparar cenários como atribuição de grupo. Esse script do PowerShell pode ser anexado a um Agendador de tarefas e implantado na mesma caixa que executa o agente de provisionamento.  

#### <a name="which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles"></a>Quais APIs do workday a solução usa para consultar e atualizar os perfis de trabalho do workday?

Atualmente, a solução usa as seguintes APIs do workday:

* Get_Workers (v21.1) para obter informações dos trabalhadores
* Maintain_Contact_Information (v26.1) para a funcionalidade Work Email Writeback
* Update_Workday_Account (v31.2) para a funcionalidade Writeback username

#### <a name="can-i-configure-my-workday-hcm-tenant-with-two-azure-ad-tenants"></a>Posso configurar meu locatário do workday HCM com dois locatários do Azure AD?

Sim, há suporte para essa configuração. Aqui estão as etapas de alto nível para configurar este cenário:

* Implante o agente de provisionamento #1 e registre-o com o #1 de locatário do Azure AD.
* Implante o agente de provisionamento #2 e registre-o com o #2 de locatário do Azure AD.
* Com base nos "domínios filho" que cada agente de provisionamento gerenciará, configure cada agente com os domínios. Um agente pode lidar com vários domínios.
* No portal do Azure, configure o WORKDAY para o aplicativo de provisionamento de usuário do AD em cada locatário e configurar-o com os respectivos domínios.

#### <a name="why-workday-to-azure-ad-user-provisioning-app-is-not-supported-if-we-have-deployed-azure-ad-connect"></a>Por que o aplicativo de provisionamento de usuário "WORKDAY para o Azure AD" não tem suporte se implantamos Azure AD Connect?

Quando o AD do Azure é usado no modo híbrido (onde ele contém uma combinação de nuvem + usuários locais), é importante ter uma definição clara de "origem de autoridade". Normalmente, os cenários híbridos exigem a implantação de Azure AD Connect. Quando Azure AD Connect é implantado, o AD local é a origem da autoridade. A introdução da workday ao Azure AD Connector na combinação pode levar a uma situação em que os valores de atributo workday poderiam substituir os valores definidos por Azure AD Connect. Portanto, o uso do aplicativo de provisionamento "WORKDAY para o Azure AD" não tem suporte quando Azure AD Connect está habilitado. Em tais situações, é recomendável usar o aplicativo de provisionamento "WORKDAY para o usuário do AD" para colocar os usuários no AD local e, em seguida, sincronizá-los no Azure AD usando o Azure AD Connect.

#### <a name="how-do-i-suggest-improvements-or-request-new-features-related-to-workday-and-azure-ad-integration"></a>Como fazer sugerir melhorias ou solicitar novos recursos relacionados ao workday e à integração do Azure AD?

Seus comentários são altamente valorizados, pois eles nos ajudam a definir a direção para as versões e os aprimoramentos futuros. Congratulamo-nos com todo o feedback e encorajamo-lo a submeter a sua ideia ou sugestão de melhoria no [fórum de feedback da Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory). Para obter feedback específico relacionado com a integração do Dia de Trabalho, selecione a categoria *Aplicações SaaS* e procure usando as palavras-chave *Workday* para encontrar feedback existente relacionado com o Dia de Trabalho.

![Aplicativos SaaS UserVoice](media/workday-inbound-tutorial/uservoice_saas_apps.png)

![Workday do UserVoice](media/workday-inbound-tutorial/uservoice_workday_feedback.png)

Ao sugerir uma nova ideia, verifique se outra pessoa já sugeriu um recurso semelhante. Nesse caso, você pode votar no recurso ou na solicitação de aprimoramento. Você também pode deixar um comentário sobre seu caso de uso específico para mostrar o suporte para a ideia e demonstrar como o recurso será valioso para você também.

### <a name="provisioning-agent-questions"></a>Perguntas do agente de provisionamento

#### <a name="what-is-the-ga-version-of-the-provisioning-agent"></a>O que é a versão GA do agente de provisionamento?

* A versão GA do agente de provisionamento é 1.1.30 e superior.
* Se a versão do agente for menor que 1.1.30, você estará executando a versão de visualização pública e ela será atualizada automaticamente para a versão GA se o servidor que hospeda o agente tiver o tempo de execução do .NET 4.7.1.
  * Pode [verificar a versão .NET](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) instalada no seu servidor. Se o servidor não estiver a funcionar .NET 4.7.1, pode [descarregar e instalar .NET 4.7.1](https://support.microsoft.com/help/4033342/the-net-framework-4-7-1-offline-installer-for-windows). O agente de provisionamento será atualizado automaticamente para a versão GA após a instalação do .NET 4.7.1.

#### <a name="how-do-i-know-the-version-of-my-provisioning-agent"></a>Como fazer saber a versão do meu agente de provisionamento?

* Entre no Windows Server onde o agente de provisionamento está instalado.
* Vá ao Painel de **Controlo** -> **Desinstalar ou Alterar um** menu de Programa
* Procure a versão correspondente à entrada **Microsoft Azure AD Connect Provisioning Agent**

  ![Portal do Azure](./media/workday-inbound-tutorial/pa_version.png)

#### <a name="does-microsoft-automatically-push-provisioning-agent-updates"></a>A Microsoft envia automaticamente as atualizações do agente de provisionamento?

Sim, a Microsoft atualiza automaticamente o agente de provisionamento. Pode desativar atualizações automáticas ao parar o atualização do agente de ligação ad do Windows **Microsoft Azure AD**.

#### <a name="can-i-install-the-provisioning-agent-on-the-same-server-running-azure-ad-connect"></a>Posso instalar o agente de provisionamento no mesmo servidor que executa o Azure AD Connect?

Sim, você pode instalar o agente de provisionamento no mesmo servidor que executa o Azure AD Connect.

#### <a name="at-the-time-of-configuration-the-provisioning-agent-prompts-for-azure-ad-admin-credentials-does-the-agent-store-the-credentials-locally-on-the-server"></a>No momento da configuração, o agente de provisionamento solicita as credenciais de administrador do Azure AD. O agente armazena as credenciais localmente no servidor?

Durante a configuração, o agente de provisionamento solicita as credenciais de administrador do Azure AD somente para se conectar ao seu locatário do Azure AD. Ele não armazena as credenciais localmente no servidor. No entanto, mantém as credenciais utilizadas para se ligar ao *domínio ative diretório no local* num cofre de senha seletiva do Windows local.

#### <a name="how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication"></a>Como fazer configurar o agente de provisionamento para usar um servidor proxy para a comunicação HTTP de saída?

O agente de provisionamento dá suporte ao uso de proxy de saída. Pode configurá-lo editando o ficheiro **c:\Program Files\Microsoft Azure AD Connect Provisioning Agent\AADConnectProvisioningAgent.exe.config**. Adicione as seguintes linhas, na extremidade do ficheiro, pouco antes da etiqueta de fecho `</configuration>`.
Substitua as variáveis [proxy-server] e [proxy-port] pelo nome do servidor proxy e valores de porta.

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

Também pode verificar se tem todas as portas necessárias abertas abrindo a ferramenta de teste de portas de [conector](https://aadap-portcheck.connectorporttest.msappproxy.net/) a partir da sua rede de instalações. Mais marcas de verificação verde significa maior resiliência.

Para certificar-se de que a ferramenta dá-lhe os resultados certos, certifique-se de que para:

* Abra a ferramenta em um navegador do servidor no qual você instalou o agente de provisionamento.
* Certifique-se de que quaisquer proxies ou firewalls aplicáveis ao agente de provisionamento também sejam aplicados a esta página. Isto pode ser feito no Internet Explorer indo para **Definições -> Opções de Internet -> Conexões -> Definições LAN**. Nessa página, você verá o campo "usar um servidor proxy para sua LAN". Selecione essa caixa e coloque o endereço do proxy no campo "endereço".

#### <a name="can-one-provisioning-agent-be-configured-to-provision-multiple-ad-domains"></a>Um agente de provisionamento pode ser configurado para provisionar vários domínios do AD?

Sim, um agente de provisionamento pode ser configurado para lidar com vários domínios do AD, contanto que o agente tenha a linha de visão para os controladores de domínio respectivos. A Microsoft recomenda configurar um grupo de 3 agentes de provisionamento que atendem o mesmo conjunto de domínios do AD para garantir a alta disponibilidade e fornecer suporte ao failover.

#### <a name="how-do-i-de-register-the-domain-associated-with-my-provisioning-agent"></a>Como fazer de registrar o domínio associado ao meu agente de provisionamento?

* Do portal Azure, obtenha a *identificação* do inquilino do seu inquilino Azure AD.
* Entre no Windows Server que executa o agente de provisionamento.
* Abra o PowerShell como administrador do Windows.
* Mude para o diretório que contenha os scripts de inscrição e execute os seguintes comandos substituindo o id\] de identificação do inquilino \[pelo valor do seu ID de inquilino.

  ```powershell
  cd “C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\RegistrationPowershell\Modules\PSModulesFolder”
  Import-Module "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\RegistrationPowershell\Modules\PSModulesFolder\AppProxyPSModule.psd1"
  Get-PublishedResources -TenantId "[tenant ID]"
  ```

* Da lista de agentes que aparecem – copie o valor do campo `id` a partir desse recurso cujo nome de *recurso* é igual ao seu nome de domínio AD.
* Cole o valor de ID nesse comando e execute o comando no PowerShell.

  ```powershell
  Remove-PublishedResource -ResourceId "[resource ID]" -TenantId "[tenant ID]"
  ```

* Execute novamente o assistente de configuração do agente.
* Todos os outros agentes que foram atribuídos anteriormente a esse domínio precisarão ser reconfigurados.

#### <a name="how-do-i-uninstall-the-provisioning-agent"></a>Como fazer desinstalar o agente de provisionamento?

* Entre no Windows Server onde o agente de provisionamento está instalado.
* Vá ao Painel de **Controlo** -> **Desinstalar ou Alterar um** menu de Programa
* Desinstale os seguintes programas:
  * Agente de provisionamento do Microsoft Azure AD Connect
  * Atualizador do agente do Microsoft Azure AD Connect
  * Pacote de agente de provisionamento do Microsoft Azure AD Connect

### <a name="workday-to-ad-attribute-mapping-and-configuration-questions"></a>Perguntas sobre configuração e mapeamento de atributo WORKDAY para AD

#### <a name="how-do-i-back-up-or-export-a-working-copy-of-my-workday-provisioning-attribute-mapping-and-schema"></a>Como fazer fazer backup ou exportar uma cópia de trabalho do meu esquema e mapeamento de atributos de provisionamento do workday?

Você pode usar Microsoft Graph API para exportar a configuração de provisionamento de usuário do workday. Consulte os passos da secção Exportação e Importação da configuração de mapeamento de [atributos](#exporting-and-importing-your-configuration) de fornecimento de utilizadores de trabalho para mais detalhes.

#### <a name="i-have-custom-attributes-in-workday-and-active-directory-how-do-i-configure-the-solution-to-work-with-my-custom-attributes"></a>Tenho atributos personalizados no workday e Active Directory. Como fazer configurar a solução para trabalhar com meus atributos personalizados?

A solução dá suporte a atributos personalizados de workday e Active Directory. Para adicionar os seus atributos personalizados ao esquema de mapeamento, abra a lâmina de **Mapeamento do Atributo** e desça para expandir a secção **Mostrar opções avançadas**. 

![Editar lista de atributos](./media/workday-inbound-tutorial/wd_edit_attr_list.png)

Para adicionar os seus atributos personalizados do Workday, selecione a lista de *atributos* editar a opção para o Workday e adicionar os seus atributos AD personalizados, selecione a lista de atributos editar opção *para On Premises Ative Directory*.

Veja também:

* [Personalizando a lista de atributos do utilizador do Dia do Trabalho](#customizing-the-list-of-workday-user-attributes)

#### <a name="how-do-i-configure-the-solution-to-only-update-attributes-in-ad-based-on-workday-changes-and-not-create-any-new-ad-accounts"></a>Como fazer configurar a solução para atualizar apenas os atributos no AD com base nas alterações do workday e não criar novas contas do AD?

Esta configuração pode ser conseguida definindo as Ações de **Objecto-Alvo** na lâmina de Mapeamento de **Atributos,** como mostrado abaixo:

![Atualizar ação](./media/workday-inbound-tutorial/wd_target_update_only.png)

Marque a caixa de seleção "atualizar" para que apenas as operações de atualização fluam do WORKDAY para o AD. 

#### <a name="can-i-provision-users-photo-from-workday-to-active-directory"></a>Posso provisionar a foto do usuário do WORKDAY para Active Directory?

A tualmente, a solução não suporta a definição de atributos binários, como *miniaturaSPhoto* e *jpegPhoto* em Diretório Ativo.

#### <a name="how-do-i-sync-mobile-numbers-from-workday-based-on-user-consent-for-public-usage"></a>Como fazer sincronizar números de celular do workday com base no consentimento do usuário para uso público?

* Vá para a folha "provisionamento" do seu aplicativo de provisionamento do workday.
* Clique nos mapeamentos de atributo 
* Em **Mapeamentos**, **selecione Synchronize Workday Workers to On Premises Ative Directory** (ou **Synchronize Workday Workers to Azure AD**).
* Na página Mapeamentos de atributo, role para baixo e marque a caixa "Mostrar opções avançadas".  Clique na lista de **atributos editar para o Dia de Trabalho**
* Na lâmina que se abre, localize o atributo "Mobile" e clique na linha para que possa editar a **Expressão API** ![RGPD Móvel](./media/workday-inbound-tutorial/mobile_gdpr.png)

* Substitua a **Expressão API** pela seguinte nova expressão, que recupera o número móvel de trabalho apenas se a "Bandeira de Uso Público" estiver definida como "Verdadeira" no Dia do Trabalho.

    ```
     wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Contact_Data/wd:Phone_Data[translate(string(wd:Phone_Device_Type_Reference/@wd:Descriptor),'abcdefghijklmnopqrstuvwxyz','ABCDEFGHIJKLMNOPQRSTUVWXYZ')='MOBILE' and translate(string(wd:Usage_Data/wd:Type_Data/wd:Type_Reference/@wd:Descriptor),'abcdefghijklmnopqrstuvwxyz','ABCDEFGHIJKLMNOPQRSTUVWXYZ')='WORK' and string(wd:Usage_Data/@wd:Public)='1']/@wd:Formatted_Phone
    ```

* Salve a lista de atributos.
* Salve o mapeamento de atributo.
* Desmarque o estado atual e reinicie a sincronização completa.

#### <a name="how-do-i-format-display-names-in-ad-based-on-the-users-departmentcountrycity-attributes-and-handle-regional-variances"></a>Como fazer nomes de exibição de formato no AD com base nos atributos de departamento/país/cidade do usuário e manipulam variações regionais?

É um requisito comum configurar o atributo de nome de *exibição* em AD de modo a que também forneça informações sobre o departamento do utilizador e país/região. Por exemplo, se John Smith trabalhar no Departamento de Marketing dos EUA, talvez queira que o seu nome de *exibição* apareça como *Smith, John (Marketing-US)* .

Aqui está como você pode lidar com tais requisitos para construir *CN* ou *displayName* para incluir atributos como empresa, unidade de negócio, cidade ou país/região.

* Cada atributo do Dia de Trabalho é recuperado usando uma expressão API XPATH subjacente, que é configurável em **Attribute Mapping -> Secção Avançada -> Editar lista de atributos para o Dia de Trabalho**. Aqui está a expressão padrão XPATH API para o Workday *PreferredFirstName*, *PreferredLastName*, *Company* and *SupervisoryOrganization* atribui.

     | Atributo workday | Expressão XPATH da API |
     | ----------------- | -------------------- |
     | Nomepreferido | wd:Trabalhador/wd:Worker_Data/wd:Personal_Data/wd:Name_Data/wd:Preferred_Name_Data/wd:Name_Detail_Data/wd:First_Name/text() |
     | PreferredLastName | wd:Trabalhador/wd:Worker_Data/wd:Personal_Data/wd:Name_Data/wd:Preferred_Name_Data/wd:Name_Detail_Data/wd:Last_Name/text() |
     | Empresa | wd:Worker/wd:Worker_Data/wd:Organization_Data/wd:Worker_Organization_Data[wd:Organization_Data/wd:Organization_Type_Reference/wd:ID [@wd:type='Organization_Type_ID'='Empresa']/wd:Organization_Reference/@wd:Descriptor |
     | SupervisoryOrganization | wd:Trabalhador/wd:Worker_Data/wd:Organization_Data/wd:Worker_Organization_Data/wd:Organization_Data[wd:Organization_Type_Reference/wd:ID[@wd:type='Organization_Type_ID'='Supervisy']/wd:Organization_Name/text() |
  
   Confirme com sua equipe do workday que a expressão de API acima é válida para sua configuração de locatário do workday. Se necessário, pode editá-los conforme descrito na secção [Personalizando a lista de atributos do utilizador do Dia](#customizing-the-list-of-workday-user-attributes)de Trabalho .

* Da mesma forma, as informações do país presentes no Dia de Trabalho são recuperadas utilizando o seguinte XPATH: *wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference*

     Há cinco atributos relacionados ao país que estão disponíveis na seção da lista de atributos do workday.

     | Atributo workday | Expressão XPATH da API |
     | ----------------- | -------------------- |
     | CountryReference | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/wd:ID [@wd:type='ISO_3166-1_Alpha-3_Code']/text() |
     | CountryReferenceFriendly | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/@wd:Descriptor |
     | CountryReferenceNumeric | wd:Trabalhador/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/wd:ID [@wd:type='ISO_3166-1_Numeric-3_Code']/texto() |
     | CountryReferenceTwoLetter | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/wd:ID [@wd:type='ISO_3166-1_Alpha-2_Code']/text() |
     | CountryRegionReference | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Region_Reference/@wd:Descriptor |

  Confirme com sua equipe do workday que as expressões de API acima são válidas para sua configuração de locatário do workday. Se necessário, pode editá-los conforme descrito na secção [Personalizando a lista de atributos do utilizador do Dia](#customizing-the-list-of-workday-user-attributes)de Trabalho .

* Para criar a expressão de mapeamento de atributo correta, identifique qual atributo workday "autoritativamente" representa o nome do usuário, o sobrenome, o país/região e o departamento. Digamos que os atributos são *PreferredFirstName*, *PreferredLastName*, *CountryReferenceTwoLetter* e *SupervisoryOrganization,* respectivamente. Pode usá-lo para construir uma expressão para o atributo ad nome de *exibição* DaD da seguinte forma para obter um nome de exibição como *Smith, John (Marketing-US)* .

    ```
     Append(Join(", ",[PreferredLastName],[PreferredFirstName]), Join(""," (",[SupervisoryOrganization],"-",[CountryReferenceTwoLetter],")"))
    ```
    Assim que tiver a expressão certa, edite a tabela de Mapeamentos de Atributos e modifique o mapeamento do atributo *do displayName* como mostrado abaixo: ![DisplayName Mapping](./media/workday-inbound-tutorial/wd_displayname_map.png)

* Estendendo o exemplo acima, digamos que gostaria de converter nomes da cidade vindos do Workday em valores abreviados e depois usá-lo para construir nomes de exibição como *Smith, John (CHI)* ou *Doe, Jane (NYC),* então este resultado pode ser alcançado usando uma expressão Switch com o *atributo do Workday Município* como a variável determinante.

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
  * [Sintaxe de função switch](../app-provisioning/functions-for-customizing-application-data.md#switch)
  * [Aderir à Sintaxe de Função](../app-provisioning/functions-for-customizing-application-data.md#join)
  * [Sintaxe de Função apêndice](../app-provisioning/functions-for-customizing-application-data.md#append)

#### <a name="how-can-i-use-selectuniquevalue-to-generate-unique-values-for-samaccountname-attribute"></a>Como posso usar SelectUniqueValue para gerar valores exclusivos para o atributo samAccountName?

Digamos que pretende gerar valores únicos para atributo *samAccountName* usando uma combinação de atributos *FirstName* e *LastName* do Workday. Veja abaixo uma expressão com a qual você pode começar:

```
SelectUniqueValue(
    Replace(Mid(Replace(NormalizeDiacritics(StripSpaces(Join("",  Mid([FirstName],1,1), [LastName]))), , "([\\/\\\\\\[\\]\\:\\;\\|\\=\\,\\+\\*\\?\\<\\>])", , "", , ), 1, 20), , "(\\.)*$", , "", , ),
    Replace(Mid(Replace(NormalizeDiacritics(StripSpaces(Join("",  Mid([FirstName],1,2), [LastName]))), , "([\\/\\\\\\[\\]\\:\\;\\|\\=\\,\\+\\*\\?\\<\\>])", , "", , ), 1, 20), , "(\\.)*$", , "", , ),
    Replace(Mid(Replace(NormalizeDiacritics(StripSpaces(Join("",  Mid([FirstName],1,3), [LastName]))), , "([\\/\\\\\\[\\]\\:\\;\\|\\=\\,\\+\\*\\?\\<\\>])", , "", , ), 1, 20), , "(\\.)*$", , "", , )
)
```

Como a expressão acima funciona: se o usuário for John Smith, ele primeiro tentará gerar JSmith, se o JSmith já existir, ele gerará JoSmith, se existir, gerará JohSmith. A expressão também garante que o valor gerado satisfaz a restrição de comprimento e a restrição de caracteres especiais associada ao *samAccountName*.

Veja também:

* [Sintaxe de Função Média](../app-provisioning/functions-for-customizing-application-data.md#mid)
* [Substituir a Sintaxe de Função](../app-provisioning/functions-for-customizing-application-data.md#replace)
* [SelectUniqueValue Function Syntax](../app-provisioning/functions-for-customizing-application-data.md#selectuniquevalue)

#### <a name="how-do-i-remove-characters-with-diacritics-and-convert-them-into-normal-english-alphabets"></a>Como fazer remover caracteres com sinais diacríticos e convertê-los em alfabetos normais em inglês?

Utilize a função [NormalizeDiacritics](../app-provisioning/functions-for-customizing-application-data.md#normalizediacritics) para remover caracteres especiais em primeiro nome e apelido do utilizador, enquanto constrói o endereço de e-mail ou o valor NC para o utilizador.

## <a name="troubleshooting-tips"></a>Sugestões de resolução de problemas

Esta seção fornece diretrizes específicas sobre como solucionar problemas de provisionamento com sua integração do workday usando os logs de auditoria do Azure AD e os logs de Visualizador de Eventos do Windows Server. Baseia-se em cima dos passos genéricos de resolução de problemas e conceitos capturados no [Tutorial: Reportagem sobre o fornecimento automático](../app-provisioning/check-status-user-account-provisioning.md) de conta de utilizador

Esta seção aborda os seguintes aspectos da solução de problemas:

* [Configuração do Espectador de Eventos do Windows para resolução de problemas do agente](#setting-up-windows-event-viewer-for-agent-troubleshooting)
* [Criação de registos de auditoria do portal Azure para resolução de problemas de serviço](#setting-up-azure-portal-audit-logs-for-service-troubleshooting)
* [A compreensão dos registos para a Conta de Utilizador AD cria operações](#understanding-logs-for-ad-user-account-create-operations)
* [Compreensão de registos para operações de atualização do Gestor](#understanding-logs-for-manager-update-operations)
* [Resolução de erros comummente encontrados](#resolving-commonly-encountered-errors)

### <a name="setting-up-windows-event-viewer-for-agent-troubleshooting"></a>Configurando o Windows Visualizador de Eventos para solução de problemas do agente

* Entre no computador do Windows Server em que o agente de provisionamento está implantado
* Abra a aplicação de ambiente de trabalho do **Espectador do Evento do Windows Server.**
* Selecione **Registos windows > Aplicação**.
* Utilize o registo de corrente de **filtro...** opção de ver todos os eventos registados sob a fonte **AAD. Connect.ProvisioningAgent** e excluir eventos com ID do evento "5", especificando o filtro "-5" como mostrado abaixo.

  ![Visualizador de Eventos do Windows](media/workday-inbound-tutorial/wd_event_viewer_01.png))

* Clique em **OK** e separe a visualização do resultado por parte da coluna **Date and Time.**

### <a name="setting-up-azure-portal-audit-logs-for-service-troubleshooting"></a>Configurando portal do Azure logs de auditoria para solução de problemas de serviço

* Lance o [portal Azure](https://portal.azure.com)e navegue para a secção de **registos** de Auditoria da sua aplicação de provisionamento do Dia de Trabalho.
* Utilize o botão **Colunas** na página Registos de Auditoria para visualizar apenas as seguintes colunas na vista (Data, Atividade, Estado, Razão de Estado). Essa configuração garante que você se concentre apenas nos dados que são relevantes para a solução de problemas.

  ![Colunas do log de auditoria](media/workday-inbound-tutorial/wd_audit_logs_00.png)

* Utilize os parâmetros de consulta **de Destino** e **Data Range** para filtrar a vista. 
  * Defina o parâmetro de consulta **do target** para o "ID do trabalhador" ou "IDENTIFICAÇÃO do empregado" do objeto de trabalhador do Dia de Trabalho.
  * Detete o Intervalo de **Data** para um período de tempo adequado durante o qual pretende investigar por erros ou problemas com o provisionamento.

  ![Filtros de log de auditoria](media/workday-inbound-tutorial/wd_audit_logs_01.png)

### <a name="understanding-logs-for-ad-user-account-create-operations"></a>Noções básicas sobre logs para operações de criação de conta de usuário do AD

Quando é detetada uma nova contratação no Workday (digamos com o Id *21023*do Empregado), o serviço de prestação de serviços da Azure AD tenta criar uma nova conta de utilizador aD para o trabalhador e no processo cria 4 registos de registos de auditoria conforme descrito abaixo:

  [registo de auditoria ![criar operações](media/workday-inbound-tutorial/wd_audit_logs_02.png)](media/workday-inbound-tutorial/wd_audit_logs_02.png#lightbox)

Quando clica em qualquer um dos registos de registos de auditoria, a página **Dedetalhes** de Atividade sabre. Aqui está o que a página Detalhes da **Atividade** mostra para cada tipo de registo de registo.

* Registo de **importação de dia de trabalho:** Este registo de registo mostra as informações dos trabalhadores recolhidas no Dia de Trabalho. Utilize informações na secção *Detalhes Adicionais* do registo de registo para resolver problemas com a recolha de dados do Dia do Trabalho. Um registro de exemplo é mostrado abaixo, juntamente com ponteiros sobre como interpretar cada campo.

  ```JSON
  ErrorCode : None  // Use the error code captured here to troubleshoot Workday issues
  EventName : EntryImportAdd // For full sync, value is "EntryImportAdd" and for delta sync, value is "EntryImport"
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID (usually the Worker ID or Employee ID field)
  SourceAnchor : a071861412de4c2486eb10e5ae0834c3 // set to the WorkdayID (WID) associated with the record
  ```

* Registo de **importação ad:** Este registo de registo mostra informações da conta recolhida a partir de AD. Como durante a criação inicial do utilizador não existe uma conta AD, a *Razão do Estado de Atividade* indicará que nenhuma conta com o valor do atributo de ID correspondente foi encontrada no Diretório Ativo. Utilize informações na secção *Detalhes Adicionais* do registo de registo para resolver problemas com a recolha de dados do Dia do Trabalho. Um registro de exemplo é mostrado abaixo, juntamente com ponteiros sobre como interpretar cada campo.

  ```JSON
  ErrorCode : None // Use the error code captured here to troubleshoot Workday issues
  EventName : EntryImportObjectNotFound // Implies that object was not found in AD
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID
  ```

  Para encontrar registos de registos de registos do Agente de Provisionamento correspondentes a esta operação de importação de AD, abra os registos do Windows Event Viewer e utilize o **Find...** opção do menu para encontrar entradas de registo contendo o valor do atributo de identificação/união de propriedade correspondente (neste caso *21023*).

  ![Localizar](media/workday-inbound-tutorial/wd_event_viewer_02.png)

  Procure a entrada com *ID do evento = 9*, que lhe fornecerá o filtro de pesquisa LDAP utilizado pelo agente para recuperar a conta AD. Você pode verificar se esse é o filtro de pesquisa correto para recuperar entradas de usuário exclusivas.

  ![Pesquisa LDAP](media/workday-inbound-tutorial/wd_event_viewer_03.png)

  O registo que o segue imediatamente com ID do *evento = 2* captura o resultado da operação de pesquisa e se devolveu quaisquer resultados.

  ![Resultados LDAP](media/workday-inbound-tutorial/wd_event_viewer_04.png)

* Registo de ação da regra da **sincronização:** Este registo de registo apresenta os resultados das regras de mapeamento do atributo e filtros de deteção configurados, juntamente com a ação de provisionamento que será tomada para processar o evento de trabalho que se aproxima. Utilize informações na secção *Detalhes Adicionais* do registo de registo saque a problemas com a ação de sincronização. Um registro de exemplo é mostrado abaixo, juntamente com ponteiros sobre como interpretar cada campo.

  ```JSON
  ErrorCode : None // Use the error code captured here to troubleshoot sync issues
  EventName : EntrySynchronizationAdd // Implies that the object will be added
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID
  SourceAnchor : a071861412de4c2486eb10e5ae0834c3 // set to the WorkdayID (WID) associated with the profile in Workday
  ```

  Se houver problemas com as expressões de mapeamento de atributo ou se os dados de entrada do workday tiverem problemas (por exemplo: valor vazio ou nulo para os atributos necessários), você observará uma falha neste estágio com o ErrorCode fornecendo detalhes da falha.

* **Registo de exportação aD:** Este registo de registo apresenta o resultado da operação de criação de conta AD juntamente com os valores de atributo que foram definidos no processo. Utilize informações na secção *Detalhes Adicionais* do registo de registo para resolver problemas com a operação de criação de conta. Um registro de exemplo é mostrado abaixo, juntamente com ponteiros sobre como interpretar cada campo. Na seção "detalhes adicionais", o "EventName" está definido como "EntryExportAdd", "Unindoproperty" é definido como o valor do atributo de ID correspondente, "SourceAnchor" é definido como WorkdayID (WID) associado ao registro e "TargetAnchor" é definido como o valor do atributo "objectGUID" do AD do usuário recém-criado. 

  ```JSON
  ErrorCode : None // Use the error code captured here to troubleshoot AD account creation issues
  EventName : EntryExportAdd // Implies that object will be created
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID
  SourceAnchor : a071861412de4c2486eb10e5ae0834c3 // set to the WorkdayID (WID) associated with the profile in Workday
  TargetAnchor : 83f0156c-3222-407e-939c-56677831d525 // set to the value of the AD "objectGuid" attribute of the new user
  ```

  Para encontrar registos de registos de registos do Agente de Provisionamento correspondentes a esta operação de exportação de AD, abra os registos do Windows Event Viewer e utilize o **Find...** opção do menu para encontrar entradas de registo contendo o valor do atributo de identificação/união de propriedade correspondente (neste caso *21023*).  

  Procure um registo HTTP POST correspondente ao carimbo de tempo da operação de exportação com *ID do evento = 2*. Esse registro conterá os valores de atributo enviados pelo serviço de provisionamento para o agente de provisionamento.

  [![SCIM Adicionar](media/workday-inbound-tutorial/wd_event_viewer_05.png)](media/workday-inbound-tutorial/wd_event_viewer_05.png#lightbox)

  Imediatamente após o evento acima, deve haver outro evento que captura a resposta da operação criar conta do AD. Esse evento retorna o novo objectGUID criado no AD e é definido como o atributo TargetAnchor no serviço de provisionamento.

  [![SCIM Adicionar](media/workday-inbound-tutorial/wd_event_viewer_06.png)](media/workday-inbound-tutorial/wd_event_viewer_06.png#lightbox)

### <a name="understanding-logs-for-manager-update-operations"></a>Noções básicas sobre logs para operações de atualização do Gerenciador

O atributo Manager é um atributo de referência no AD. O serviço de provisionamento não define o atributo Manager como parte da operação de criação do usuário. Em vez disso, o atributo do gestor é definido como parte de uma operação de *atualização* após a criação da conta AD para o utilizador. Expandindo o exemplo acima, digamos que um novo contrato com id "21451" é ativado no Workday e o novo gestor de contratação *(21023)* já tem uma conta AD. Nesse cenário, a pesquisa dos logs de auditoria para o usuário 21451 mostra 5 entradas.

  [Atualização do gestor de ![](media/workday-inbound-tutorial/wd_audit_logs_03.png)](media/workday-inbound-tutorial/wd_audit_logs_03.png#lightbox)

Os quatro primeiros registros são como aqueles que exploramos como parte da operação de criação do usuário. O quinto registro é a exportação associada à atualização de atributo do Gerenciador. O registo de registo mostra o resultado da operação de atualização do gestor de conta AD, que é realizada utilizando o atributo *objectGuid* do gestor.

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
* [Erros de criação de conta de utilizador aD](#ad-user-account-creation-errors)
* [Erros de atualização da conta de utilizador aD](#ad-user-account-update-errors)

#### <a name="provisioning-agent-errors"></a>Erros do agente de provisionamento

|#|Cenário de erro |Causas prováveis|Resolução recomendada|
|--|---|---|---|
|1.| Erro ao instalar o agente de provisionamento com uma mensagem de erro: *O serviço 'Microsoft Azure AD Connect Provisioning Agent' (AADConnectProvisioningAgent) não começou. Verifique se tem privilégios suficientes para iniciar o sistema.* | Esse erro geralmente aparece se você estiver tentando instalar o agente de provisionamento em um controlador de domínio e a diretiva de grupo impedir que o serviço seja iniciado.  Ele também será visto se você tiver uma versão anterior do agente em execução e não tiver desinstalado antes de iniciar uma nova instalação.| Instale o agente de provisionamento em um servidor não DC. Verifique se as versões anteriores do agente foram desinstaladas antes de instalar o novo agente.|
|2.| O Serviço Windows 'Microsoft Azure AD Connect Provisioning Agent' *encontra-se* em estado de arranque e não muda para o estado *de execução.* | Como parte da instalação, o assistente do agente cria uma conta local (**Serviço NT\\AADConnectProvisioningAgent**) no servidor e esta é a conta de logon utilizada para iniciar o serviço. Se uma política de segurança no seu Windows Server impedir que contas locais executem os serviços, você encontrará esse erro. | Abra a *consola Services.* Clique com o botão direito do mouse no serviço do Windows ' Microsoft Azure AD conectar agente de provisionamento ' e, na guia logon, especifique a conta de um administrador de domínio para executar o serviço. Reinicie o serviço. |
|3.| Ao configurar o agente de provisionamento com o seu domínio AD no passo *Connect Ative Directory,* o assistente demora muito tempo a tentar carregar o esquema AD e, eventualmente, vezes vezes. | Geralmente, este erro aparece se o assistente não conseguir contactar o servidor de controlador de domínio do AD devido a problemas na firewall. | No ecrã do assistente do *Diretório Ativo Connect,* ao mesmo tempo que fornece as credenciais para o seu domínio AD, existe uma opção chamada Prioridade do controlador de *domínio Select*. Use esta opção para selecionar um controlador de domínio que esteja no mesmo site que o servidor do agente e verifique se não há nenhuma regra de firewall bloqueando a comunicação. |

#### <a name="connectivity-errors"></a>Erros de conectividade

Se o serviço de provisionamento não puder se conectar ao workday ou Active Directory, isso poderá fazer com que o provisionamento entre em um estado em quarentena. Use a tabela abaixo para solucionar problemas de conectividade.

|#|Cenário de erro |Causas prováveis|Resolução recomendada|
|--|---|---|---|
|1.| Ao clicar na Ligação de **Teste,** obtém a mensagem de erro: Houve um erro de *ligação ao Diretório Ativo. Certifique-se de que o Agente de Provisionamento no local está em funcionamento e está configurado com o domínio de Diretório Ativo correto.* | Esse erro geralmente aparece se o agente de provisionamento não estiver em execução ou se houver um firewall bloqueando a comunicação entre o Azure AD e o agente de provisionamento. Você também poderá ver esse erro se o domínio não estiver configurado no assistente de agente. | Abra a consola *Serviços* no servidor Windows para confirmar que o agente está em execução. Abra o assistente do agente de provisionamento e confirme se o domínio correto está registrado no agente.  |
|2.| O trabalho de provisionamento entra em estado de quarentena nos fins de semana (Sex-Sáb) e recebemos uma notificação por email de que há um erro com a sincronização. | Uma das causas comuns deste erro é o período de indisponibilidade planeado do Workday. Se estiver a utilizar um inquilino de implementação do Workday, tenha em conta que o Workday tem um período de indisponibilidade agendado para os inquilinos de implementação (normalmente, da noite de sexta-feira à manhã de sábado) e, durante esse período, as aplicações de aprovisionamento do Workday podem entrar em quarentena, pois não se conseguem ligar ao Workday. Regressam ao estado normal quando o inquilino de implementação do Workday estiver novamente online. Em casos raros, também poderá ver este erro se a palavra-passe de Utilizador de Sistema de Integração tiver sido alterada devido a uma atualização do inquilino ou se a conta estiver bloqueada ou expirada. | Verifique junto do seu administrador do Workday ou do seu parceiro de integração qual é o período de indisponibilidade do Workday, de modo a ignorar as mensagens de alerta durante esse período e confirmar a disponibilidade assim que a instância do Workday estiver de novo online.  |


#### <a name="ad-user-account-creation-errors"></a>Erros de criação de conta de usuário do AD

|#|Cenário de erro |Causas prováveis|Resolução recomendada|
|--|---|---|---|
|1.| Falhas de operação de exportação no registo de auditoria com o erro da *mensagem: OperaçõesError-SvcErr: Ocorreu um erro de operação. Não foi configurada nenhuma referência superior para o serviço de diretório. O serviço de diretório não pode, portanto, emitir referências a objetos fora desta floresta.* | Este erro geralmente aparece se o Recipiente de *Diretório Ativo* OU não for definido corretamente ou se houver problemas com o Mapeamento de Expressão usado para *o nome distinto*dos pais . | Verifique se há tipometro de contentor de *diretório ativo* OU. Se estiver a utilizar *parentDistinguishedName* no mapeamento de atributos, confirme que é sempre avaliado para um contentor conhecido dentro do domínio do AD. Verifique o evento *Export* nos registos de auditoria para ver o valor gerado. |
|2.| Falhas de operação de exportação no registo de auditoria com código de erro: *SystemForCrossDomainIdentityManagementBadResponse* e *error de mensagem: ConstraintViolation-AtrErr: Um valor no pedido é inválido. Um valor para o atributo não estava na gama aceitável de valores. \nErros Detalhes: CONSTRAINT_ATT_TYPE - empresa*. | Embora este erro seja específico do atributo da *empresa,* pode ver este erro para outros atributos como *o CN* também. Esse erro aparece devido à restrição de esquema AD imposta. Por padrão, os atributos como *a empresa* e a *CN* em AD têm um limite superior de 64 caracteres. Se o valor proveniente do workday tiver mais de 64 caracteres, você verá essa mensagem de erro. | Verifique o evento *exportação* nos registos de auditoria para ver o valor do atributo relatado na mensagem de erro. Considere truncar o valor proveniente do Dia do Trabalho utilizando a função [Mid](../app-provisioning/functions-for-customizing-application-data.md#mid) ou alterando os mapeamentos para um atributo ad que não tenha restrições de comprimento semelhantes.  |

#### <a name="ad-user-account-update-errors"></a>Erros de atualização da conta de usuário do AD

Durante o processo de atualização da conta de usuário do AD, o serviço de provisionamento lê as informações do workday e do AD, executa as regras de mapeamento de atributos e determina se qualquer alteração precisa entrar em vigor. Portanto, um evento Update é disparado. Se qualquer uma dessas etapas encontrar uma falha, ela será registrada nos logs de auditoria. Use a tabela a seguir para solucionar erros comuns de atualização.

|#|Cenário de erro |Causas prováveis|Resolução recomendada|
|--|---|---|---|
|1.| Falhas de ação da regra de sincronização no registo de auditoria com o Nome de evento da mensagem *= EntrySynchronizationError and ErrorCode = EndpointInavailable*. | Esse erro aparecerá se o serviço de provisionamento não puder recuperar dados de perfil de usuário do Active Directory devido a um erro de processamento encontrado pelo agente de provisionamento local. | Verifique o agente de provisionamento Visualizador de Eventos logs para eventos de erro que indicam problemas com a operação de leitura (filtrar por ID do evento #2). |
|2.| O atributo Manager no AD não é atualizado para determinados usuários no AD. | A causa mais provável desse erro é se você estiver usando regras de escopo e o gerente do usuário não fizer parte do escopo. Você também poderá encontrar esse problema se o atributo de ID correspondente do Gerenciador (por exemplo, EmployeeID) não for encontrado no domínio do AD de destino ou não estiver definido como o valor correto. | Examine o filtro de escopo e adicione o usuário do Gerenciador no escopo. Verifique o perfil do gerente no AD para certificar-se de que há um valor para o atributo de ID correspondente. |

## <a name="managing-your-configuration"></a>Gerenciando sua configuração

Esta seção descreve como você pode estender, personalizar e gerenciar ainda mais sua configuração de provisionamento de usuário controlada pelo workday. Ele aborda os seguintes tópicos:

* [Personalizando a lista de atributos do utilizador do Dia do Trabalho](#customizing-the-list-of-workday-user-attributes)  
* [Exportação e importação da sua configuração](#exporting-and-importing-your-configuration)

### <a name="customizing-the-list-of-workday-user-attributes"></a>Personalizando a lista de atributos de usuário do workday

Os aplicativos de provisionamento do WORKDAY para Active Directory e o Azure AD incluem uma lista padrão de atributos de usuário do workday que você pode selecionar. No entanto, essas listas não são abrangentes. O workday dá suporte a muitas centenas de atributos de usuário possíveis, que podem ser padrão ou exclusivos para seu locatário do workday.

O serviço de provisionamento de AD Azure suporta a capacidade de personalizar a sua lista ou atributo do Dia de Trabalho para incluir quaisquer atributos expostos na [operação Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) da API de Recursos Humanos.

Para esta alteração, deve utilizar o [Workday Studio](https://community.workday.com/studio-download) para extrair as expressões XPath que representam os atributos que pretende utilizar e, em seguida, adicioná-las à sua configuração de provisionamento utilizando o editor de atributoavançado no portal Azure.

**Para recuperar uma expressão XPath para um atributo do utilizador do Dia de Trabalho:**

1. Descarregue e instale [o Estúdio Workday.](https://community.workday.com/studio-download) Você precisará de uma conta da Comunidade do WORKDAY para acessar o instalador.

2. Descarregue o ficheiro WSDL do Workday Human_Resources a partir deste URL: https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Human_Resources.wsdl

3. Inicie o workday Studio.

4. A partir da barra de comando, selecione o **Workday > Test Web Service na** opção Tester.

5. Selecione **External**, e selecione o ficheiro WSDL Human_Resources descarregado no passo 2.

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio1.png)

6. Desloque o campo **de localização** para `https://IMPL-CC.workday.com/ccx/service/TENANT/Human_Resources`, mas substituindo "IMPL-CC" pelo seu tipo de instância real, e "TENANT" pelo seu nome real de inquilino.

7. Definir **operação** para **Get_Workers**

8.  Clique no pequeno link **configurado** abaixo das vidraças De Pedido/Resposta para definir as suas credenciais do Dia de Trabalho. Verifique a **Autenticação**e, em seguida, introduza o nome de utilizador e a palavra-passe para a sua conta do sistema de integração workday. Certifique-se de formatar o nome de utilizador como nome\@inquilino e deixar a opção **UsernameToken de Segurança WS** selecionada.

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio2.png)

9. Selecione **OK**.

10. No painel **Request,** cola no XML abaixo e define **Employee_ID** para a identificação do empregado de um verdadeiro utilizador no seu inquilino do Dia de Trabalho. Selecione um usuário que tenha o atributo populado que você deseja extrair.

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

11. Clique no **Pedido de Envio** (seta verde) para executar o comando. Se for bem sucedida, a resposta deve figurar no painel **resposta.** Verifique a resposta para garantir que ela tenha os dados da ID de usuário inserida e não um erro.

12. Se for bem sucedido, copie o XML do painel **resposta** e guarde-o como um ficheiro XML.

13. Na barra de comando do Estúdio Workday, selecione **File > Open File...** e abra o ficheiro XML que guardou. Essa ação abrirá o arquivo no editor de XML do workday Studio.

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio3.png)

14. Na árvore de arquivo, navegue através de **/env: Envelope > env: Body > wd:Get_Workers_Response > wd:Response_Data > wd: Worker** to find your user's data.

15. Em **wd: Trabalhador,** encontre o atributo que pretende adicionar e selecione-o.

16. Copie a expressão XPath para o seu atributo selecionado fora do campo **Document Path.**

17. Retire o **/env:Envelope/env:Body/wd:Get_Workers_Response/wd:Response_Data/** prefixo da expressão copiada.

18. Se o último item da expressão copiada for um nó (exemplo: "/wd: Birth_Date"), então **anexar/texto()** no final da expressão. Isto não é necessário se o último item for um atributo (exemplo: "/@wd: tipo").

19. O resultado deve ser algo como `wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()`. Esse valor é o que você vai copiar para o portal do Azure.

**Para adicionar o seu atributo personalizado do utilizador do Dia de Trabalho à sua configuração de provisionamento:**

1. Lance o [portal Azure](https://portal.azure.com)e navegue até à secção de provisionamento da sua aplicação de provisionamento workday, como descrito anteriormente neste tutorial.

2. Desemposição do estado de **provisionamento** para **desligar**e selecione **Guardar**. Esta etapa ajudará a garantir que as alterações entrarão em vigor somente quando você estiver pronto.

3. Em **Mapeamentos**, **selecione Synchronize Workday Workers to On Premises Ative Directory** (ou **Synchronize Workday Workers to Azure AD**).

4. Percorra para a parte inferior do próximo ecrã e selecione **Mostrar opções avançadas**.

5. Selecione **editar lista de atributos para O Dia de Trabalho**.

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio_aad1.png)

6. Role até a parte inferior da lista de atributos para onde estão os campos de entrada.

7. Para **Nome,** introduza um nome de exibição para o seu atributo.

8. Para **Tipo**, selecione o tipo que corresponde adequadamente ao seu atributo **(a corda** é mais comum).

9. Para **expressão API,** introduza a expressão XPath que copiou do Workday Studio. Exemplo: `wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()`

10. **Selecione Adicionar Atributo**.

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio_aad2.png)

11. Selecione **Guardar** acima e, em seguida, **sim** ao diálogo. Feche a tela de mapeamento de atributo se ela ainda estiver aberta.

12. De volta ao separador principal de **provisionamento,** selecione **Synchronize Workday Workers to On Premises Ative Directory** (ou **Synchronize Workers to Azure AD**) novamente.

13. **Selecione Adicionar novo mapeamento**.

14. O seu novo atributo deve agora figurar na lista de **atributos Fonte.**

15. Adicione um mapeamento para o novo atributo conforme desejado.

16. Quando terminar, lembre-se de voltar a colocar o **Estado de Provisionamento** **em On** e guardar.

### <a name="exporting-and-importing-your-configuration"></a>Exportar e importar configuração

Consulte o artigo Configuração de [fornecimento de exportação e importação](../app-provisioning/export-import-provisioning-configuration.md)

## <a name="managing-personal-data"></a>Gerir dados pessoais

A solução de provisionamento do WORKDAY para Active Directory requer que um agente de provisionamento seja instalado em um servidor Windows local, e esse agente cria logs no log de eventos do Windows que podem conter dados pessoais, dependendo do seu atributo do WORKDAY para o AD mapeamentos. Para estar em conformidade com as obrigações de privacidade do usuário, você pode garantir que nenhum dado seja retido nos logs de eventos além de 48 horas, configurando uma tarefa agendada do Windows para limpar o log de eventos.

O serviço de provisionamento da AD Azure insere-se na categoria de processador de **dados** da classificação rGPD. Como um pipeline de processador de dados, o serviço fornece serviços de processamento de dados para parceiros-chave e consumidores finais. O serviço de provisionamento do Azure AD não gera dados de usuário e não tem controle independente sobre quais dados pessoais são coletados e como eles são usados. A recuperação de dados, a agregação, a análise e os relatórios no serviço de provisionamento do Azure AD são baseados em dados corporativos existentes.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-hybrid-note.md)]

Em relação à retenção de dados, o serviço de provisionamento do Azure AD não gera relatórios, executa análises ou fornece informações além de 30 dias. Portanto, o serviço de provisionamento do Azure AD não armazena, processa nem retém dados além de 30 dias. Esse design é compatível com as normas de GDPR, os regulamentos de conformidade de privacidade da Microsoft e as políticas de retenção de dados do Azure AD.

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)
* [Saiba como configurar um único sign-on entre o Workday e o Azure Ative Directory](workday-tutorial.md)
* [Saiba como integrar outras aplicações do SaaS com o Diretório Ativo Azure](tutorial-list.md)
* [Saiba como usar APIs do Microsoft Graph para gerir configurações de provisionamento](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)
