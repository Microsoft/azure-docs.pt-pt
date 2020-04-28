---
title: 'Tutorial: Configure Workday para fornecimento automático de utilizadores com Diretório Ativo Azure [ Microsoft Docs'
description: Aprenda a configurar o Diretório Ativo Azure para fornecer automaticamente e desfornecer contas de utilizador ao Dia do Trabalho.
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
ms.date: 04/23/2020
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6a816f2235fa5356f2300255ec9d2fb2b315acf7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82190321"
---
# <a name="tutorial-configure-workday-for-automatic-user-provisioning"></a>Tutorial: Configure workday para fornecimento automático de utilizadores

O objetivo deste tutorial é mostrar os passos necessários para importar perfis de trabalhadores do Workday para o Ative Directory e o Azure Ative Directory, com redação opcional de endereço de e-mail e nome de utilizador para o Workday.

## <a name="overview"></a>Descrição geral

O serviço de prestação de [utilizadores do Azure Ative Directory](../app-provisioning/user-provisioning.md) integra-se com a API de Recursos Humanos do Dia do [Trabalho,](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) a fim de fornecer contas de utilizador. A Azure AD utiliza esta ligação para permitir os seguintes fluxos de trabalho de fornecimento de utilizadores:

* **Provisioning users to Ative Directory** - Provision selected sets of users from Workday to one or more Ative Directory domains.

* **Disponibilizando utilizadores apenas na nuvem ao Diretório Ativo Do Azure** - Em cenários em que não é utilizado o Ative Directory no local, os utilizadores podem ser aprovisionados diretamente do Workday para o Azure Ative Directory utilizando o serviço de provisionamento de utilizadores da AD Azure.

* **Escreva de volta o endereço de e-mail e o nome** de utilizador para o Workday - O serviço de fornecimento de utilizadores da AD Azure pode escrever os endereços de e-mail e o nome de utilizador de Azure AD de volta ao Workday.

### <a name="what-human-resources-scenarios-does-it-cover"></a>Que cenários de recursos humanos cobre?

Os fluxos de trabalho de fornecimento de utilizadores do Workday suportados pelo serviço de prestação de utilizadores da AD Azure permitem a automatização dos seguintes recursos humanos e cenários de gestão do ciclo de vida identitário:

* **Contratação** de novos colaboradores - Quando um novo colaborador é adicionado ao Workday, uma conta de utilizador é automaticamente criada em Ative Directory, Azure Ative Directory, e opcionalmente Office 365 e [outras aplicações SaaS suportadas pela Azure AD,](../app-provisioning/user-provisioning.md)com redação do endereço de e-mail para o Workday.

* **Atributo sipróprio e atualizações** de perfil - Quando um registo de empregados é atualizado no Workday (como o seu nome, título ou gestor), a sua conta de utilizador será automaticamente atualizada em Diretório Ativo, Diretório Ativo Azure e opcionalmente Office 365 e [outras aplicações SaaS suportadas pela Azure AD](../app-provisioning/user-provisioning.md).

* **Rescisões** de funcionários - Quando um trabalhador é despedido no Workday, a sua conta de utilizador é automaticamente desativada em Diretório Ativo, Diretório Ativo Azure e opcionalmente Office 365 e [outras aplicações SaaS suportadas pela Azure AD.](../app-provisioning/user-provisioning.md)

* **Recontratações** de empregados - Quando um empregado é recontratado no Workday, a sua conta antiga pode ser automaticamente reativada ou represtada (dependendo da sua preferência) ao Ative Directory, Ao Diretório Ativo azure e opcionalmente ao Office 365 e [a outras aplicações saaS apoiadas pela Azure AD](../app-provisioning/user-provisioning.md).

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Para quem é esta solução de fornecimento de utilizadores mais adequada?

Esta solução de fornecimento de utilizadores do Workday é ideal para:

* Organizações que desejem uma solução pré-construída e baseada na nuvem para o fornecimento de utilizadores do Dia de Trabalho

* Organizações que exigem o fornecimento direto de utilizadores do Dia do Trabalho para O Diretório Ativo, ou Diretório Ativo Azure

* Organizações que exigem que os utilizadores sejam aprovisionados utilizando dados obtidos a partir do módulo HCM do dia de trabalho (ver [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html))

* Organizações que exigem a adesão, mudança e deixando os utilizadores sincronizados com uma ou mais Florestas de Diretórios Ativos, Domínios e OUs com base apenas em informações de mudança detetadas no módulo HCM do Dia de Trabalho (ver [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html))

* Organizações que usam o Office 365 para e-mail

## <a name="solution-architecture"></a>Arquitetura de Soluções

Esta secção descreve a arquitetura de solução de solução de fornecimento de utilizadores de ponta a ponta para ambientes híbridos comuns. Existem dois fluxos relacionados:

* Fluxo de **dados de RH autoritário – do Dia de Trabalho ao Diretório Ativo no local:** Neste fluxo de eventos de trabalhadores (tais como Novas Contratações, Transferências, Rescisões) ocorrem primeiro na nuvem Workday HR inquilino e, em seguida, os dados do evento fluem para o diretório ativo no local através da Azure AD e do Agente de Provisionamento. Dependendo do evento, pode levar a operações de criação/atualização/ativação/desativação em AD.
* **Email e Username Writeback Flow – do Diretório Ativo no local para o Dia** do Trabalho: Uma vez que a criação da conta esteja completa no Ative Directory, é sincronizada com azure AD Através do Azure AD Connect e o e-mail e o atributo de nome de utilizador podem ser reescritos ao Workday.

![Descrição geral](./media/workday-inbound-tutorial/wd_overview.png)

### <a name="end-to-end-user-data-flow"></a>Fluxo de dados de utilizador de ponta a ponta

1. A equipa de RH realiza transações de trabalhadores (Joiners/Movers/Leavers ou New Hires/Transfers/Terminations) no Workday HCM
2. O Serviço de Provisionamento de AD Azure executa sincronizações programadas de identidades do Workday HR e identifica alterações que precisam de ser processadas para sincronização com o Diretório Ativo no local.
3. O Serviço de Provisionamento de AD Azure invoca o agente de provisionamento de ligação Azure AD no local com uma carga útil de pedido contendo operações de criação/atualização/ativação/desativação da conta AD.
4. O Agente de Provisionamento de Ligação AD Azure utiliza uma conta de serviço para adicionar/atualizar dados da conta AD.
5. O motor Azure AD Connect / AD Sync executa a delta sync para puxar atualizações em AD.
6. As atualizações do Diretório Ativo estão sincronizadas com o Diretório Ativo azure.
7. Se o conector Workday Writeback estiver configurado, escreve o atributo de e-mail e o nome de utilizador para o Workday, com base no atributo correspondente utilizado.

## <a name="planning-your-deployment"></a>Planejando a sua implantação

Antes de iniciar a sua integração no Dia do Trabalho, verifique os pré-requisitos abaixo e leia as seguintes orientações sobre como combinar os requisitos atuais de arquitetura de Diretório Ativo e de fornecimento de utilizadores com as soluções fornecidas pelo Azure Ative Directory. Um plano de [implementação](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-plans) abrangente com folhas de cálculo de planeamento também está disponível para ajudá-lo a colaborar com o seu parceiro de integração Workday e partes interessadas em RH.

Esta secção abrange os seguintes aspetos de planeamento:

* [Pré-requisitos](#prerequisites)
* [Selecionando aplicações de conector de provisionamento para implementar](#selecting-provisioning-connector-apps-to-deploy)
* [Planejamento da implantação do Agente de Provisionamento de Ligação AD Azure](#planning-deployment-of-azure-ad-connect-provisioning-agent)
* [Integração com vários domínios de Diretório Ativo](#integrating-with-multiple-active-directory-domains)
* [Planejamento do dia de trabalho para o utilizador de diretório ativo atribui mapeamento e transformações](#planning-workday-to-active-directory-user-attribute-mapping-and-transformations)

### <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes itens:

* Uma licença de subscrição Válida Azure AD Premium P1 ou superior para cada utilizador que será proveniente do Workday e aprovisionada no diretório ativo no local ou no Diretório Ativo Azure.
* Acesso global de administrador global da Azure AD para configurar o agente de provisionamento
* Um inquilino de implementação do Dia de Trabalho para fins de teste e integração
* Permissões do administrador no Workday para criar um utilizador de integração do sistema, e fazer alterações para testar os dados dos empregados para fins de teste
* Para o fornecimento de utilizadores ao Ative Directory, é necessário um servidor que execute o Windows Server 2012 ou superior com o tempo de funcionamento .NET 4.7.1+ para acolher o agente de [provisionamento no local.](https://go.microsoft.com/fwlink/?linkid=847801)
* [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) para sincronizar utilizadores entre Diretório Ativo e Azure AD

### <a name="selecting-provisioning-connector-apps-to-deploy"></a>Selecionando aplicações de conector de provisionamento para implementar

Para facilitar o fornecimento de fluxos de trabalho entre o Workday e o Ative Directory, a Azure AD fornece várias aplicações de conector de provisionamento que pode adicionar na galeria de aplicações Azure AD:

![Galeria de aplicações Azure AD](./media/workday-inbound-tutorial/wd_gallery.png)

* **Workday to Ative Directory User Provisioning** - Esta aplicação facilita o fornecimento de conta de utilizador do Workday para um único domínio de Diretório Ativo. Se tiver vários domínios, pode adicionar uma instância desta aplicação a partir da galeria de aplicações Azure AD para cada domínio de Diretório Ativo a que precisa fornecer.

* **Workday to Azure AD User Provisioning** - Embora o Azure AD Connect seja a ferramenta que deve ser usada para sincronizar utilizadores de Diretório Ativo para o Diretório Ativo do Azure, esta aplicação pode ser usada para facilitar o fornecimento de utilizadores apenas na nuvem desde o Dia de Trabalho a um único inquilino do Diretório Ativo Azure.

* **Workday Writeback** - Esta aplicação facilita a redação dos endereços de e-mail do utilizador do Azure Ative Directory para o Workday.

> [!TIP]
> A aplicação regular "Workday" é utilizada para a criação de um único sign-on entre o Workday e o Azure Ative Directory.

Utilize o gráfico de fluxo de decisão abaixo para identificar quais as aplicações de provisionamento do Dia do Trabalho relevantes para o seu cenário.
    ![Fluxograma de Decisão](./media/workday-inbound-tutorial/wday_app_flowchart.png "Fluxograma de Decisão")

Utilize a tabela de conteúdos para ir à secção relevante deste tutorial.

### <a name="planning-deployment-of-azure-ad-connect-provisioning-agent"></a>Planejamento da implantação do Agente de Provisionamento de Ligação AD Azure

> [!NOTE]
> Esta secção só é relevante se planeia implementar o Workday para a App de Provisionamento de Utilizadores de Diretório Ativo. Pode ignorar isto se estiver a implementar a Workday Writeback ou workday para a App de Provisionamento de Utilizadores de Anúncios Azure.

A solução workday to AD User Provisioning requer a implementação de um ou mais Agentes de Provisionamento em servidores que executam o Windows 2012 R2 ou superior com um tempo mínimo de 4 GB de RAM e .NET 4.7.1+. Devem ser tomadas em consideração as seguintes considerações antes da instalação do Agente de Provisionamento:

* Certifique-se de que o servidor anfitrião que executa o Agente de Provisionamento tem acesso à rede ao domínio AD alvo
* O Assistente de Configuração do Agente de Provisionamento regista o agente com o seu inquilino Azure AD e o processo de registo requer acesso a *.msappproxy.net sobre a porta TLS 443. Certifique-se de que existem regras de firewall de saída que permitam esta comunicação. O agente suporta a configuração de [procuração HTTPS de saída](#how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication).
* O Agente de Provisionamento utiliza uma conta de serviço para comunicar com o domínio ad(s) no local. Antes da instalação do agente, recomenda-se que crie uma conta de serviço com permissões de administrador de domínio e uma palavra-passe que não expire.  
* Durante a configuração do Agente de Provisionamento, pode selecionar controladores de domínio que devem lidar com pedidos de provisionamento. Se tiver vários controladores de domínio distribuídos geograficamente, instale o Agente de Provisionamento no mesmo local que o seu(s) controlador de domínio preferido para melhorar a fiabilidade e desempenho da solução de ponta a ponta
* Para uma elevada disponibilidade, pode implementar mais do que um Agente de Provisionamento e registá-lo para lidar com o mesmo conjunto de domínios ad.

> [!IMPORTANT]
> Em ambientes de produção, a Microsoft recomenda que tenha um mínimo de 3 Agentes de Provisionamento configurados com o seu inquilino Azure AD para alta disponibilidade.

### <a name="integrating-with-multiple-active-directory-domains"></a>Integração com vários domínios de Diretório Ativo

> [!NOTE]
> Esta secção só é relevante se planeia implementar o Workday para a App de Provisionamento de Utilizadores de Diretório Ativo. Pode ignorar isto se estiver a implementar a Workday Writeback ou workday para a App de Provisionamento de Utilizadores de Anúncios Azure.

Dependendo da sua topologia de Diretório Ativo, terá de decidir o número de aplicações de conector de fornecimento de utilizadores e o número de Agentes de Provisionamento para configurar. Listados abaixo estão alguns dos padrões comuns de implantação a que pode consultar à medida que planeia a sua implantação.

#### <a name="deployment-scenario-1--single-workday-tenant---single-ad-domain"></a>Cenário de implantação #1 : Inquilino único -> domínio único da AD

Neste cenário, você tem um inquilino Workday e você gostaria de fornecer utilizadores para um domínio ad único alvo. Aqui está a configuração de produção recomendada para esta implementação.

|   |   |
| - | - |
| Não. de agentes de provisionamento para implantar no local | 3 (para alta disponibilidade e falha) |
| Não. de Workday para Aplicações de Provisionamento de Utilizadores AD para configurar no portal Azure | 1 |

  ![Cenário 1](./media/workday-inbound-tutorial/dep_scenario1.png)

#### <a name="deployment-scenario-2--single-workday-tenant---multiple-child-ad-domains"></a>Cenário de implantação #2 : Inquilino único -> domínios ad várias crianças

Este cenário envolve o fornecimento de utilizadores desde o Dia de Trabalho até vários domínios de crianças ad-alvo numa floresta. Aqui está a configuração de produção recomendada para esta implementação.

|   |   |
| - | - |
| Não. de agentes de provisionamento para implantar no local | 3 (para alta disponibilidade e falha) |
| Não. de Workday para Aplicações de Provisionamento de Utilizadores AD para configurar no portal Azure | uma aplicação por domínio infantil |

  ![Cenário 2](./media/workday-inbound-tutorial/dep_scenario2.png)

#### <a name="deployment-scenario-3--single-workday-tenant---disjoint-ad-forests"></a>Cenário de implantação #3 : Inquilino único -> florestas disconjuntas

Este cenário envolve o fornecimento de utilizadores do Dia do Trabalho para domínios em florestas de AD desarticuladas. Aqui está a configuração de produção recomendada para esta implementação.

|   |   |
| - | - |
| Não. de agentes de provisionamento para implantar no local | 3 por floresta disarticulada da AD |
| Não. de Workday para Aplicações de Provisionamento de Utilizadores AD para configurar no portal Azure | uma aplicação por domínio infantil |

  ![Cenário 3](./media/workday-inbound-tutorial/dep_scenario3.png)

### <a name="planning-workday-to-active-directory-user-attribute-mapping-and-transformations"></a>Planejamento do dia de trabalho para o utilizador de diretório ativo atribui mapeamento e transformações

> [!NOTE]
> Esta secção só é relevante se planeia implementar o Workday para a App de Provisionamento de Utilizadores de Diretório Ativo. Pode ignorar isto se estiver a implementar a Workday Writeback ou workday para a App de Provisionamento de Utilizadores de Anúncios Azure.

Antes de configurar o fornecimento do utilizador a um domínio de Diretório Ativo, considere as seguintes questões. As respostas a estas perguntas determinarão como é que os filtros de deteção e os mapeamentos de atributos precisam de ser definidos.

* **Que utilizadores no Workday precisam de ser aprovisionados a esta floresta de Diretório Ativo?**

  * *Exemplo: Utilizadores onde o atributo "Empresa" do Dia de Trabalho contém o valor "Contoso", e o atributo "Worker_Type" contém "Regular"*

* **Como é que os utilizadores são encaminhados para diferentes unidades de organização (OUs)?**

  * *Exemplo: Os utilizadores são encaminhados para OUs que correspondem a uma localização de escritório, conforme definido nos atributos "Município" e "Country_Region_Reference"*

* **Como devem ser preenchidos os seguintes atributos no Diretório Ativo?**

  * Nome Comum (cn)
    * *Exemplo: Utilize o valor User_ID do Dia de Trabalho, conforme definido pelos recursos humanos*

  * ID do empregado (funcionárioId)
    * *Exemplo: Use o valor Worker_ID do Dia de Trabalho*

  * Nome da conta SAM (SAMAccountName)
    * *Exemplo: Utilize o valor do dia de trabalho User_ID, filtrado através de uma expressão de fornecimento de anúncios Azure para remover caracteres ilegais*

  * Nome principal do utilizador (userPrincipalName)
    * *Exemplo: Utilize o valor User_ID do Dia de Trabalho, com uma expressão de fornecimento de Anúncios Azure para anexar um nome de domínio*

* **Como devem os utilizadores ser acompanhados entre o Workday e o Ative Directy?**

  * *Exemplo: Os utilizadores com um valor "Worker_ID" específico do Workday são combinados com utilizadores de Diretório Ativo onde o "employeeID" tem o mesmo valor. Se o valor Worker_ID não for encontrado no Diretório Ativo, crie um novo utilizador.*
  
* **A floresta de Diretório Ativo já contém as IDs do utilizador necessárias para que a lógica correspondente funcione?**

  * *Exemplo: Se esta configuração for uma nova implementação do Dia do Trabalho, recomenda-se que o Diretório Ativo seja pré-povoado com os valores Worker_ID do Dia de Trabalho corretos (ou valor único de id de eleição) para manter a lógica correspondente o mais simples possível.*

Como configurar e configurar estas aplicações especiais de conector é o tema das restantes secções deste tutorial. Quais as aplicações que escolher configurar dependerá dos sistemas a que precisa fornecer, e de quantos domínios de Diretório Ativo e inquilinos da AD Azure estão no seu ambiente.

## <a name="configure-integration-system-user-in-workday"></a>Configure o utilizador do sistema de integração no Dia do Trabalho

Um requisito comum de todos os conectores de provisionamento do Dia de Trabalho é que eles exigem credenciais de um utilizador do sistema de integração workday para se conectar à API de Recursos Humanos do Dia do Trabalho. Esta secção descreve como criar um utilizador do sistema de integração no Dia do Trabalho e tem as seguintes secções:

* [Criação de um utilizador do sistema de integração](#creating-an-integration-system-user)
* [Criação de um grupo de segurança de integração](#creating-an-integration-security-group)
* [Configurar permissões de política de segurança de domínio](#configuring-domain-security-policy-permissions)
* [Configurar permissões de política de segurança de processos empresariais](#configuring-business-process-security-policy-permissions)
* [Ativar mudanças na política de segurança](#activating-security-policy-changes)

> [!NOTE]
> É possível contornar este procedimento e, em vez disso, utilizar uma conta de administrador global workday como conta de integração do sistema. Isto pode funcionar bem para demonstrações, mas não é recomendado para implantações de produção.

### <a name="creating-an-integration-system-user"></a>Criação de um utilizador do sistema de integração

**Para criar um utilizador do sistema de integração:**

1. Inscreva-se no seu inquilino do Dia do Trabalho usando uma conta de administrador. Na **Aplicação Workday,** introduza o utilizador na caixa de pesquisa e, em seguida, clique em Criar utilizador do Sistema de **Integração**.

   ![Criar utilizador](./media/workday-inbound-tutorial/wd_isu_01.png "Criar utilizador")
2. Complete a tarefa do Utilizador do **Sistema de Integração Criar** fornecendo um nome de utilizador e uma palavra-passe para um novo Utilizador do Sistema de Integração.  
  
   * Deixe a **nova palavra-passe requerida na** opção Seguinte Sinal in sem verificação, porque este utilizador irá iniciar sessão programáticamente.
   * Deixe o **Timeout Minutes** da Sessão com o seu valor padrão de 0, o que impedirá que as sessões do utilizador se cronometrem prematuramente.
   * Selecione a opção Não permitir que as **UI Sessions,** uma vez que fornece uma camada adicional de segurança que impede um utilizador de aceder à palavra-passe do sistema de integração para iniciar sessão no Workday.

   ![Criar o Utilizador do Sistema de Integração](./media/workday-inbound-tutorial/wd_isu_02.png "Criar o Utilizador do Sistema de Integração")

### <a name="creating-an-integration-security-group"></a>Criação de um grupo de segurança de integração

Neste passo, irá criar um grupo de segurança do sistema de integração sem restrições ou limitado no Workday e atribuir o utilizador do sistema de integração criado no passo anterior a este grupo.

**Para criar um grupo de segurança:**

1. Introduza criar um grupo de segurança na caixa de pesquisa e, em seguida, clique em **Criar Grupo de Segurança**.

    ![Grupo CreateSecurity](./media/workday-inbound-tutorial/wd_isu_03.png "Grupo CreateSecurity")
2. Complete a tarefa do **Grupo Criar Segurança.** 

   * Existem dois tipos de grupos de segurança no Dia do Trabalho:
     * **Sem restrições:** Todos os membros do grupo de segurança podem aceder a todas as instâncias de dados protegidas pelo grupo de segurança.
     * **Constrangido:** Todos os membros do grupo de segurança têm acesso contextual a um subconjunto de instâncias de dados (linhas) a que o grupo de segurança pode aceder.
   * Consulte o seu parceiro de integração Workday para selecionar o tipo de grupo de segurança apropriado para a integração.
   * Assim que conhecer o tipo de grupo, selecione **Integration System Security Group (Unconstrained)** ou Integration System Security Group **(Constranged)** do Tipo de Dropdown do **Grupo de Segurança Inquilino.**

     ![Grupo CreateSecurity](./media/workday-inbound-tutorial/wd_isu_04.png "Grupo CreateSecurity")

3. Após a criação do Grupo de Segurança ser bem sucedida, você verá uma página onde você pode atribuir membros ao Grupo de Segurança. Adicione o novo utilizador do sistema de integração criado no passo anterior a este grupo de segurança. Se estiver a utilizar um grupo de segurança *constrangido,* também terá de selecionar o âmbito de organização apropriado.

    ![Editar Grupo de Segurança](./media/workday-inbound-tutorial/wd_isu_05.png "Editar Grupo de Segurança")

### <a name="configuring-domain-security-policy-permissions"></a>Configurar permissões de política de segurança de domínio

Neste passo, você concederá permissões políticas de "segurança de domínio" para os dados dos trabalhadores ao grupo de segurança.

**Para configurar as permissões da política de segurança do domínio:**

1. Introduza a **configuração** de segurança do domínio na caixa de pesquisa e, em seguida, clique no relatório de configuração de **configuração**de domínio do link .  

    ![Políticas de Segurança do Domínio](./media/workday-inbound-tutorial/wd_isu_06.png "Políticas de Segurança do Domínio")  
2. Na caixa de texto **domínio,** procure os seguintes domínios e adicione-os ao filtro um a um.  
   * *Provisionamento de Conta Externa*
   * *Dados dos trabalhadores: Trabalhadores*
   * *Dados dos trabalhadores: Relatórios dos trabalhadores públicos*
   * *Dados da Pessoa: Informações de contato de trabalho*
   * *Dados do Trabalhador: Todas as Posições*
   * *Dados dos Trabalhadores: Informação atual sobre pessoal*
   * *Dados do Trabalhador: Título de negócio no perfil do trabalhador*
   * *Contas do dia de trabalho*
   
     ![Políticas de Segurança do Domínio](./media/workday-inbound-tutorial/wd_isu_07.png "Políticas de Segurança do Domínio")  

     ![Políticas de Segurança do Domínio](./media/workday-inbound-tutorial/wd_isu_08.png "Políticas de Segurança do Domínio") 

     Clique em **OK**.

3. No relatório que aparece, selecione a elipse (...) que aparece ao lado do Fornecimento de **Conta Externa** e clique na opção menu **Domínio -> Editar Permissões** de Política de Segurança

    ![Políticas de Segurança do Domínio](./media/workday-inbound-tutorial/wd_isu_09.png "Políticas de Segurança do Domínio")  

4. Na página de Permissões de Política de Segurança do **Domínio de Edição,** desloque-se até à secção **Permissões**de Integração . Clique no sinal "+" para adicionar o grupo de sistema de integração à lista de grupos de segurança com permissões de integração **Get** e **Put.**

    ![Editar Permissão](./media/workday-inbound-tutorial/wd_isu_10.png "Editar Permissão")  

5. Clique no sinal "+" para adicionar o grupo de sistema de integração à lista de grupos de segurança com permissões de integração **Get** e **Put.**

    ![Editar Permissão](./media/workday-inbound-tutorial/wd_isu_11.png "Editar Permissão")  

6. Repita os passos 3-5 acima para cada uma destas políticas de segurança restantes:

   | Operação | Política de Segurança do Domínio |
   | ---------- | ---------- |
   | Obter e Colocar | Dados dos trabalhadores: Relatórios dos trabalhadores públicos |
   | Obter e Colocar | Dados da Pessoa: Informações de contato de trabalho |
   | Get | Dados dos trabalhadores: Trabalhadores |
   | Get | Dados do Trabalhador: Todas as Posições |
   | Get | Dados dos Trabalhadores: Informação atual sobre pessoal |
   | Get | Dados do Trabalhador: Título de negócio no perfil do trabalhador |
   | Obter e Colocar | Contas do dia de trabalho |

### <a name="configuring-business-process-security-policy-permissions"></a>Configurar permissões de política de segurança de processos empresariais

Neste passo, você concederá permissões políticas de "segurança de processos de negócio" para os dados dos trabalhadores para o grupo de segurança. Este passo é necessário para a configuração do conector da aplicação Workday Writeback.

**Para configurar permissões de política de segurança de processos empresariais:**

1. Introduza a Política de **Processo de Negócios** na caixa de pesquisa e clique no link Editar a tarefa de Política de Segurança do **Processo de Negócios.**  

    ![Políticas de segurança do processo de negócio](./media/workday-inbound-tutorial/wd_isu_12.png "Políticas de segurança do processo de negócio")  

2. Na caixa de texto **do Tipo de Processo de Negócios,** *procure* contacto e selecione Processo de troca de contactos de **trabalho** alterar o processo de negócio e clique em **OK**.

    ![Políticas de segurança do processo de negócio](./media/workday-inbound-tutorial/wd_isu_13.png "Políticas de segurança do processo de negócio")  

3. Na página política de segurança do processo de **negócio seleção,** percorra a secção **Change Work Contact Information (Serviço Web).**
    

4. Selecione e adicione o novo grupo de segurança do sistema de integração à lista de grupos de segurança que podem iniciar o pedido de serviços web. 

    ![Políticas de segurança do processo de negócio](./media/workday-inbound-tutorial/wd_isu_15.png "Políticas de segurança do processo de negócio")  

5. Clique em **Done**. 

### <a name="activating-security-policy-changes"></a>Ativar mudanças na política de segurança

**Para ativar alterações de política de segurança:**

1. Introduza ativar na caixa de pesquisa e, em seguida, clique no link **Ativar alterações**de política de segurança pendente .

    ![Ativar](./media/workday-inbound-tutorial/wd_isu_16.png "Ativar")

1. Inicie a tarefa de ativação da política de segurança pendente, inserindo um comentário para fins de auditoria e, em seguida, clique em **OK**.
1. Complete a tarefa no ecrã seguinte, verificando a caixa de verificação **Confirme,** e, em seguida, clique **em OK**.

    ![Ativar a Segurança Pendente](./media/workday-inbound-tutorial/wd_isu_18.png "Ativar a Segurança Pendente")  

## <a name="configuring-user-provisioning-from-workday-to-active-directory"></a>Configurar o fornecimento de utilizadores do Dia do Trabalho para o Diretório Ativo

Esta secção fornece passos para o fornecimento de conta de utilizador do Workday para cada domínio de Diretório Ativo no âmbito da sua integração.

* [Adicione a aplicação de conector de provisionamento e descarregue o Agente de Provisionamento](#part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent)
* [Instalar e configurar no local agente de provisionamento(s)](#part-2-install-and-configure-on-premises-provisioning-agents)
* [Configure a conectividade com o Workday e o Ative Directy](#part-3-in-the-provisioning-app-configure-connectivity-to-workday-and-active-directory)
* [Configure mapeamentos de atributos](#part-4-configure-attribute-mappings)
* [Ativar e lançar o fornecimento de utilizadores](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent"></a>Parte 1: Adicione a aplicação de conector de provisionamento e descarregue o Agente de Provisionamento

**Para configurar o Workday para o fornecimento de Diretório Ativo:**

1. Aceda a <https://portal.azure.com>.

2. No portal Azure, procure e selecione **Azure Ative Directory**.

3. Selecione **Aplicações Empresariais,** em **seguida, todas as aplicações**.

4. Selecione **Adicionar uma aplicação**e selecione a categoria **All.**

5. Procure o **Workday Provisioning para Ative Directory**e adicione essa aplicação a partir da galeria.

6. Depois da adição da aplicação e do ecrã de detalhes da aplicação, selecione **Provisioning**.

7. Mude o **modo** **de provisionamento** para **automático**.

8. Clique no banner de informação apresentado para descarregar o Agente de Provisionamento. 

   ![Agente de descarregamento](./media/workday-inbound-tutorial/pa-download-agent.png "Descarregue o ecrã do agente")


### <a name="part-2-install-and-configure-on-premises-provisioning-agents"></a>Parte 2: Instalar e configurar no local agente de provisionamento ou s

Para fornecer ao Diretório Ativo no local, o agente de provisionamento deve ser instalado num servidor que tenha .NET 4.7.1+ Enquadramento e acesso à rede ao domínio de Diretório Ativo pretendido.

> [!TIP]
> Pode verificar a versão da estrutura .NET no seu servidor utilizando as instruções [aqui](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed)fornecidas .
> Se o servidor não tiver .NET 4.7.1 ou mais instalado, pode descarregá-lo a partir [daqui](https://support.microsoft.com/help/4033342/the-net-framework-4-7-1-offline-installer-for-windows).  

Transfira o instalador de agente descarregado para o anfitrião do servidor e siga os passos indicados abaixo para completar a configuração do agente.

1. Faça sessão no Windows Server onde pretende instalar o novo agente.

1. Lance o instalador do Agente de Provisionamento, concorde com os termos e clique no botão **Instalar.**

   ![Instalar ecrã](./media/workday-inbound-tutorial/pa_install_screen_1.png "Instalar ecrã")
   
1. Após a instalação estar concluída, o assistente será lançado e verá o ecrã **AD Connect Azure.** Clique no botão **Authenticate** para ligar à sua instância Azure AD.

   ![Ligar ao Azure AD](./media/workday-inbound-tutorial/pa_install_screen_2.png "Ligar ao Azure AD")
   
1. Autenticar a sua instância De AD Azure usando credenciais de administrador global.

   ![Administrador Auth](./media/workday-inbound-tutorial/pa_install_screen_3.png "Administrador Auth")

   > [!NOTE]
   > As credenciais de administração da Azure AD são usadas apenas para se conectarem ao seu inquilino Azure AD. O agente não armazena as credenciais localmente no servidor.

1. Após autenticação bem sucedida com a AD Azure, verá o ecrã connect **Ative Directy.** Neste passo, introduza o nome de domínio AD e clique no botão **Adicionar Diretório.**

   ![Adicionar Diretório](./media/workday-inbound-tutorial/pa_install_screen_4.png "Adicionar Diretório")
  
1. Será agora solicitado a introduzir as credenciais necessárias para se ligar ao Domínio AD. No mesmo ecrã, pode utilizar a prioridade do controlador de **domínio Select** para especificar os controladores de domínio que o agente deve utilizar para enviar pedidos de fornecimento.

   ![Credenciais de domínio](./media/workday-inbound-tutorial/pa_install_screen_5.png)
   
1. Depois de configurar o domínio, o instalador apresenta uma lista de domínios configurados. Neste ecrã, pode repetir passo #5 e #6 para adicionar mais domínios ou clicar em **Next** para proceder ao registo do agente.

   ![Domínios configurados](./media/workday-inbound-tutorial/pa_install_screen_6.png "Domínios configurados")

   > [!NOTE]
   > Se tiver vários domínios ad (por exemplo, na.contoso.com, emea.contoso.com), adicione cada domínio individualmente à lista.
   > Apenas a adição do domínio dos pais (por exemplo, contoso.com) não é suficiente. Deve registar cada domínio infantil com o agente.
   
1. Reveja os detalhes da configuração e clique em **Confirmar** para registar o agente.
  
   ![Confirmar tela](./media/workday-inbound-tutorial/pa_install_screen_7.png "Confirmar tela")
   
1. O assistente de configuração mostra o progresso do registo do agente.
  
   ![Registo do Agente](./media/workday-inbound-tutorial/pa_install_screen_8.png "Registo do Agente")
   
1. Assim que o registo do agente for bem sucedido, pode clicar na **Saída** para sair do Assistente.
  
   ![Ecrã de saída](./media/workday-inbound-tutorial/pa_install_screen_9.png "Ecrã de saída")
   
1. Verifique a instalação do Agente e certifique-se de que está a funcionar abrindo o Snap-In "Services" e procure o Serviço denominado "Microsoft Azure AD Connect Provisioning Agent"
  
   ![Serviços](./media/workday-inbound-tutorial/services.png)

### <a name="part-3-in-the-provisioning-app-configure-connectivity-to-workday-and-active-directory"></a>Parte 3: Na aplicação de provisionamento, configure a conectividade com o Workday e o Ative Diretório
Neste passo, estabelecemos conectividade com o Workday e o Ative Directy no portal Azure. 

1. No portal Azure, volte ao Workday para ative Directory User Provisioning App criado na [Parte 1](#part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent)

1. Complete a secção **de Credenciais de Administrador** da seguinte forma:

   * Nome de **utilizador workday** – Introduza o nome de utilizador da conta do sistema de integração Workday, com o nome de domínio do arrendatário anexado. Deve parecer algo como: **nome de\@utilizador tenant_name**

   * **Senha do dia de trabalho -** Insira a palavra-passe da conta do sistema de integração do Dia de Trabalho

   * **Url da API de Serviços Web de Workday –** Insira o URL no ponto final dos serviços web workday para o seu inquilino. Este valor deve https://wd3-impl-services1.workday.com/ccx/service/contoso4parecer: , onde o *contoso4* é substituído pelo seu nome de inquilino correto e *wd3-impl* é substituído pela corda ambiental correta.

     > [!NOTE]
     > Por padrão, a aplicação utiliza serviços web de dia de trabalho (WWS) v21.1 se nenhuma informação da versão for especificada no URL. Para utilizar uma versão API wws específica, utilize o formato URL:https://####.workday.com/ccx/service/tenantName/Human_Resources/v##.# <br>
     > Exemplo: https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources/v31.0 <br>
     
     > [!NOTE]
     > Se estiver a utilizar uma wws API v30.0 ou superior, antes de ligar o trabalho de provisionamento, por favor atualize as **expressões XPATH API** em **mapeamento de atributos -> Lista de atributos de Edição > Editar para** o Dia de Trabalho referindo-se à secção [Gestão da sua configuração](#managing-your-configuration) e referência de [atributo do Dia](../app-provisioning/workday-attribute-reference.md#xpath-values-for-workday-web-services-wws-api-v30)de Trabalho .  

   * Floresta de **Diretório Ativo -** O "Nome" do seu domínio de Diretório Ativo, registado no agente. Utilize a queda para selecionar o domínio alvo para o fornecimento. Este valor é tipicamente uma corda como: *contoso.com*

   * **Recipiente de Diretório Ativo -** Introduza o DN do recipiente onde o agente deve criar contas de utilizador por padrão.
        Exemplo: *OU=Utilizadores Standard,OU=Utilizadores,DC=contoso,DC=teste*
        
     > [!NOTE]
     > Esta definição só entra em jogo para criações de conta de utilizador se o atributo *parentDistinguishedName* não estiver configurado nos mapeamentos do atributo. Esta definição não é utilizada para operações de pesquisa ou atualização do utilizador. Toda a subárvore de domínio cai no âmbito da operação de busca.

   * **Email de notificação –** Insira o seu endereço de e-mail e verifique a caixa de verificação "enviar e-mail se ocorrer falha".

     > [!NOTE]
     > O Serviço de Provisionamento de AD Azure envia notificação por e-mail se o trabalho de provisionamento entrar em estado de [quarentena.](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)

   * Clique no botão **de ligação** de teste. Se o teste de ligação for bem sucedido, clique no botão **Guardar** na parte superior. Se falhar, verifique duas vezes se as credenciais do Dia de Trabalho e as credenciais aD configuradas na configuração do agente são válidas.

     ![Portal do Azure](./media/workday-inbound-tutorial/wd_1.png)

   * Uma vez que as credenciais são guardadas com sucesso, a secção **de Mapeamentos** apresentará o mapeamento padrão **Synchronize Workday Workers to On Premises Ative Directory**

### <a name="part-4-configure-attribute-mappings"></a>Parte 4: Configurar mapeamentos de atributos

Nesta secção, irá configurar como os dados dos utilizadores fluem do Workday para o Ative Directory.

1. No separador provisionamento em **Mapeamentos,** clique em **Sincronizar trabalhadores do dia**de trabalho para o diretório ativo no local .

1. No campo **Source Object Scope,** pode selecionar quais os conjuntos de utilizadores no Workday que devem estar disponíveis para o fornecimento de AD, definindo um conjunto de filtros baseados em atributos. O âmbito padrão é "todos os utilizadores no Dia do Trabalho". Filtros de exemplo:

   * Exemplo: Âmbito para utilizadores com IDs de trabalhador entre 10000000 e 2000000 (excluindo 20000000)

      * Atributo: WorkerID

      * Operador: Regex Match

      * Valor: (1[0-9][0-9][0-9][0-9][0-9][0-9]]]]

   * Exemplo: Apenas trabalhadores e não trabalhadores contingentes

      * Atributo: EmployeeID

      * Operador: NÃO É NULO

   > [!TIP]
   > Quando estiver a configurar a aplicação de provisionamento pela primeira vez, terá de testar e verificar os seus mapeamentos e expressões de atributos para se certificar de que está a dar-lhe o resultado desejado. A Microsoft recomenda a utilização dos filtros de deteção no âmbito do Âmbito do **Objeto Fonte** para testar os seus mapeamentos com alguns utilizadores de teste do Workday. Uma vez verificado que os mapeamentos funcionam, então pode remover o filtro ou expandi-lo gradualmente para incluir mais utilizadores.

   > [!CAUTION] 
   > O comportamento predefinido do motor de provisionamento é desativar/eliminar utilizadores que desapertem o seu alcance. Isto pode não ser desejável no seu Dia de Trabalho para a integração de Anúncios. Para anular este comportamento padrão, consulte o artigo [Sem a eliminação das contas dos utilizadores que saem do âmbito](../app-provisioning/skip-out-of-scope-deletions.md)
  
1. No campo **Target Object Actions,** pode filtrar globalmente quais as ações que são realizadas no Diretório Ativo. **Criar** e **Atualizar** são mais comuns.

1. Na secção de **mapeamento sacar,** pode definir como cada dia de trabalho atribui o mapa aos atributos do Diretório Ativo.

1. Clique num mapeamento de atributoexistente para atualizá-lo, ou clique **Em adicionar novo mapeamento** na parte inferior do ecrã para adicionar novos mapeamentos. Um mapeamento individual de atributos suporta estas propriedades:

      * **Tipo de mapeamento**

         * **Direto** – Escreve o valor do atributo do Dia de Trabalho ao atributo AD, sem alterações

         * **Constante** - Escreva um valor de corda estático e constante para o atributo AD

         * **Expressão** – Permite-lhe escrever um valor personalizado para o atributo AD, com base num ou mais atributos do Dia de Trabalho. [Para mais informações, consulte este artigo sobre expressões.](../app-provisioning/functions-for-customizing-application-data.md)

      * **Atributo de origem** - O atributo do utilizador do Dia de Trabalho. Se o atributo que procura não estiver presente, consulte personalizar [a lista de atributos do utilizador do Dia](#customizing-the-list-of-workday-user-attributes)de Trabalho .

      * **Valor predefinido** – Opcional. Se o atributo de origem tiver um valor vazio, o mapeamento escreverá este valor.
            A configuração mais comum é deixar isto em branco.

      * **Atributo-alvo** – O atributo do utilizador no Diretório Ativo.

      * **Combine objetos usando este atributo** – Se este mapeamento deve ou não ser usado para identificar exclusivamente os utilizadores entre o Workday e o Ative Diretório. Este valor é tipicamente definido no campo de ID do trabalhador para o Dia de Trabalho, que é tipicamente mapeado para um dos atributos de ID do empregado em Diretório Ativo.

      * **Precedência correspondente** – Podem ser definidos múltiplos atributos correspondentes. Quando existem múltiplos, são avaliados na ordem definida por este campo. Assim que um jogo é encontrado, não são avaliados mais atributos correspondentes.

      * **Aplique este mapeamento**

         * **Sempre** – Aplique este mapeamento tanto na criação do utilizador como nas ações de atualização

         * **Apenas durante** a criação - Aplicar este mapeamento apenas em ações de criação de utilizadores

1. Para guardar os seus mapeamentos, clique em **Guardar** no topo da secção Attribute-Mapping.

   ![Portal do Azure](./media/workday-inbound-tutorial/wd_2.png)

#### <a name="below-are-some-example-attribute-mappings-between-workday-and-active-directory-with-some-common-expressions"></a>Abaixo estão alguns mapeamentos de atributos de exemplo entre Workday e Ative Directy, com algumas expressões comuns

* A expressão que mapeia para o atributo *parentDistinguishedName* é usada para fornecer um utilizador a diferentes OUs com base em um ou mais atributos de origem do Dia do Trabalho. Este exemplo coloca aqui utilizadores em diferentes OUs com base na cidade em que se encontram.

* O atributo *do userPrincipalName* no Ative Directory é gerado utilizando a função de desduplicação [SelectUniqueValue](../app-provisioning/functions-for-customizing-application-data.md#selectuniquevalue) que verifica a existência de um valor gerado no domínio ad-alvo e apenas o define se for único.  

* [Há documentação sobre a escrita de expressões aqui.](../app-provisioning/functions-for-customizing-application-data.md) Esta secção inclui exemplos sobre como remover caracteres especiais.

| ATRIBUIÇÃO WORKDAY | ATRIBUIÇÃO DE DIRETÓRIO ATIVO |  IDENTIFICAÇÃO CORRESPONDENTE? | CRIAR / ATUALIZAR |
| ---------- | ---------- | ---------- | ---------- |
| **TrabalhadorID**  |  EmployeeID | **Sim** | Escrito apenas na criação |
| **Nome preferidoData**    |  cn    |   |   Escrito apenas na criação |
| **SelectUniqueValue("Join(".",\@ \[FirstName\], \[Last\]\@contoso.comName\[\] \[\]\@\[\] \[\], "contoso.com"), Join("contoso.com.**   | userPrincipalName     |     | Escrito apenas na criação 
| `Replace(Mid(Replace(\[UserID\], , "(\[\\\\/\\\\\\\\\\\\\[\\\\\]\\\\:\\\\;\\\\\|\\\\=\\\\,\\\\+\\\\\*\\\\?\\\\&lt;\\\\&gt;\])", , "", , ), 1, 20), , "([\\\\.)\*\$](file:///\\.)*$)", , "", , )`      |    sAMAccountName            |     |         Escrito apenas na criação |
| **Switch,\[\]"0", "True", "1", "False")** |  contaDeficientes      |     | Criar + atualização |
| **Primeiro nome**   | nomeDado       |     |    Criar + atualização |
| **Apelido**   |   sn   |     |  Criar + atualização |
| **Nome preferidoData**  |  displayName |     |   Criar + atualização |
| **Empresa**         | empresa   |     |  Criar + atualização |
| **Organização de Supervisão**  | departamento  |     |  Criar + atualização |
| **GestorReferência**   | gestor  |     |  Criar + atualização |
| **BusinessTitle**   |  título     |     |  Criar + atualização | 
| **Data de Endereçoline**    |  streetAddress  |     |   Criar + atualização |
| **Município**   |   l   |     | Criar + atualização |
| **PaísReferênciaTwoLetter**      |   co |     |   Criar + atualização |
| **PaísReferênciaTwoLetter**    |  c  |     |         Criar + atualização |
| **Referência countryregion** |  SC     |     | Criar + atualização |
| **WorkSpaceReference** | physicalDeliveryOfficeName    |     |  Criar + atualização |
| **CódigoPostal**  |   Código postal  |     | Criar + atualização |
| **PrimaryWorkPhone**  |  telefoneNúmero   |     | Criar + atualização |
| **Fax**      | facsimilePhoneNumber     |     |    Criar + atualização |
| **Móvel**  |    dispositivo móvel       |     |       Criar + atualização |
| **Referência local** |  língua preferida  |     |  Criar + atualização |                                               
| **Switch(\[Município,\]"OU=Utilizadores Standard,OU=Utilizadores,OU=Default,OU=Localizações,DC=contoso,DC=com", "Dallas", "OU=Utilizadores Standard,OU=Utilizadores,OU=Utilizadores,OU=Dallas,OU=Localizações,DC=contoso,DC=com", "Austin", "OU=Utilizadores Standard,OU=Utilizadores,OU=Austin,OU=Localizações,DC=contoso,DC=com, "Seattle", "OU=Utilizadores Standard,OU=Utilizadores,OU=Utilizadores,OU=Seattle,OU=Localizações,DC=contoso,DC=com", "Londres", "OU=Utilizadores Standard,OU=Utilizadores,OU=Londres,OU=Locations,DC=contoso,DC=com")**  | nome parental Distinto     |     |  Criar + atualização |

Uma vez concluída a configuração de mapeamento do atributo, pode agora [ativar e lançar o serviço](#enable-and-launch-user-provisioning)de fornecimento do utilizador .

## <a name="configuring-user-provisioning-to-azure-ad"></a>Configurar o fornecimento de utilizadores à Azure AD

As seguintes secções descrevem passos para configurar o fornecimento de utilizadores do Workday para o Azure AD para implementações apenas na nuvem.

* [Adicionar a aplicação de conector Azure AD e criar a ligação ao Dia do Trabalho](#part-1-adding-the-azure-ad-provisioning-connector-app-and-creating-the-connection-to-workday)
* [Configure Workday e Azure AD atribuem mapeamentos](#part-2-configure-workday-and-azure-ad-attribute-mappings)
* [Ativar e lançar o fornecimento de utilizadores](#enable-and-launch-user-provisioning)

> [!IMPORTANT]
> Siga apenas o procedimento abaixo se tiver utilizadores apenas na nuvem que precisam de ser aprovisionados para a AD Azure e não para o Diretório Ativo no local.

### <a name="part-1-adding-the-azure-ad-provisioning-connector-app-and-creating-the-connection-to-workday"></a>Parte 1: Adicionar a aplicação de conector Azure AD e criar a ligação ao Dia do Trabalho

**Para configurar o fornecimento de Workday para Azure Ative Directory para utilizadores apenas na nuvem:**

1. Aceda a <https://portal.azure.com>.

2. No portal Azure, procure e selecione **Azure Ative Directory**.

3. Selecione **Aplicações Empresariais,** em **seguida, todas as aplicações**.

4. Selecione **Adicionar uma aplicação**e, em seguida, selecione a categoria **All.**

5. Procure o **workday para o fornecimento de Anúncios Azure,** e adicione essa aplicação a partir da galeria.

6. Depois da adição da aplicação e do ecrã de detalhes da aplicação, selecione **Provisioning**.

7. Mude o **modo** **de provisionamento** para **automático**.

8. Complete a secção **de Credenciais de Administrador** da seguinte forma:

   * Nome de **utilizador workday** – Introduza o nome de utilizador da conta do sistema de integração Workday, com o nome de domínio do arrendatário anexado. Deve parecer algo como:username@contoso4

   * **Senha do dia de trabalho -** Insira a palavra-passe da conta do sistema de integração do Dia de Trabalho

   * **Url da API de Serviços Web de Workday –** Insira o URL no ponto final dos serviços web workday para o seu inquilino. Este valor deve https://wd3-impl-services1.workday.com/ccx/service/contoso4parecer: , onde o *contoso4* é substituído pelo seu nome de inquilino correto e *wd3-impl* é substituído pela corda ambiental correta. Se este URL não for conhecido, trabalhe com o seu parceiro de integração workday ou representante de suporte para determinar o URL correto a utilizar.

     > [!NOTE]
     > Por padrão, a aplicação utiliza o Workday Web Services v21.1 se não forespecificada nenhuma informação da versão no URL. Para utilizar uma versão API específica do Workday Web Services, utilize o formato URL:https://####.workday.com/ccx/service/tenantName/Human_Resources/v##.# <br>
     > Exemplo: https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources/v31.0


   * **Email de notificação –** Insira o seu endereço de e-mail e verifique a caixa de verificação "enviar e-mail se ocorrer falha".

   * Clique no botão **de ligação** de teste.

   * Se o teste de ligação for bem sucedido, clique no botão **Guardar** na parte superior. Se falhar, verifique duas vezes se o URL do Dia de Trabalho e as credenciais são válidas no Dia do Trabalho.

### <a name="part-2-configure-workday-and-azure-ad-attribute-mappings"></a>Parte 2: Configurar mapeamentos de atributos do Dia de Trabalho e do Azure AD

Nesta secção, irá configurar como os dados dos utilizadores fluem do Workday para o Azure Ative Directory para utilizadores apenas na nuvem.

1. No separador provisionamento em **Mapeamentos,** clique em **Sincronizar trabalhadores para Azure AD**.

2. No campo **Source Object Scope,** pode selecionar quais os conjuntos de utilizadores no Workday que devem estar disponíveis para o fornecimento ao Azure AD, definindo um conjunto de filtros baseados em atributos. O âmbito padrão é "todos os utilizadores no Dia do Trabalho". Filtros de exemplo:

   * Exemplo: Margem de manobra para utilizadores com IDs de trabalhador entre 10000000 e 2000000

      * Atributo: WorkerID

      * Operador: Regex Match

      * Valor: (1[0-9][0-9][0-9][0-9][0-9][0-9]]]]

   * Exemplo: Apenas trabalhadores contingentes e não trabalhadores regulares

      * Atributo: ContingentID

      * Operador: NÃO É NULO

3. No campo **Target Object Actions,** pode filtrar globalmente quais as ações que são realizadas em Azure AD. **Criar** e **Atualizar** são mais comuns.

4. Na secção de **mapeamento sacar,** pode definir como cada dia de trabalho atribui o mapa aos atributos do Diretório Ativo.

5. Clique num mapeamento de atributoexistente para atualizá-lo, ou clique **Em adicionar novo mapeamento** na parte inferior do ecrã para adicionar novos mapeamentos. Um mapeamento individual de atributos suporta estas propriedades:

   * **Tipo de mapeamento**

      * **Direto** – Escreve o valor do atributo do Dia de Trabalho ao atributo AD, sem alterações

      * **Constante** - Escreva um valor de corda estático e constante para o atributo AD

      * **Expressão** – Permite-lhe escrever um valor personalizado para o atributo AD, com base num ou mais atributos do Dia de Trabalho. [Para mais informações, consulte este artigo sobre expressões.](../app-provisioning/functions-for-customizing-application-data.md)

   * **Atributo de origem** - O atributo do utilizador do Dia de Trabalho. Se o atributo que procura não estiver presente, consulte personalizar [a lista de atributos do utilizador do Dia](#customizing-the-list-of-workday-user-attributes)de Trabalho .

   * **Valor predefinido** – Opcional. Se o atributo de origem tiver um valor vazio, o mapeamento escreverá este valor.
            A configuração mais comum é deixar isto em branco.

   * **Atributo-alvo** – O atributo do utilizador em Azure AD.

   * **Combine objetos usando este atributo** – Se este atributo deve ou não ser usado para identificar exclusivamente os utilizadores entre o Workday e o Azure AD. Este valor é tipicamente definido no campo de ID do trabalhador para o Dia de Trabalho, que é tipicamente mapeado para o atributo de ID do empregado (novo) ou um atributo de extensão em Azure AD.

   * **Precedência correspondente** – Podem ser definidos múltiplos atributos correspondentes. Quando existem múltiplos, são avaliados na ordem definida por este campo. Assim que um jogo é encontrado, não são avaliados mais atributos correspondentes.

   * **Aplique este mapeamento**

     * **Sempre** – Aplique este mapeamento tanto na criação do utilizador como nas ações de atualização

     * **Apenas durante** a criação - Aplicar este mapeamento apenas em ações de criação de utilizadores

6. Para guardar os seus mapeamentos, clique em **Guardar** no topo da secção Attribute-Mapping.

Uma vez concluída a configuração de mapeamento do atributo, pode agora [ativar e lançar o serviço](#enable-and-launch-user-provisioning)de fornecimento do utilizador .

## <a name="configuring-azure-ad-attribute-writeback-to-workday"></a>Configurar a AD Azure atribui a reprodução ao Dia do Trabalho

Siga estas instruções para configurar a redação dos endereços de e-mail do utilizador e do nome de utilizador do Diretório Ativo Azure para o Dia do Trabalho.

* [Adicionar a aplicação de conector Writeback e criar a ligação ao Dia do Trabalho](#part-1-adding-the-writeback-connector-app-and-creating-the-connection-to-workday)
* [Configure mapeamento sinuoso de atributos](#part-2-configure-writeback-attribute-mappings)
* [Ativar e lançar o fornecimento de utilizadores](#enable-and-launch-user-provisioning)

### <a name="part-1-adding-the-writeback-connector-app-and-creating-the-connection-to-workday"></a>Parte 1: Adicionar a aplicação de conector Writeback e criar a ligação ao Dia do Trabalho

**Para configurar o conector Workday Writeback:**

1. Aceda a <https://portal.azure.com>.

2. No portal Azure, procure e selecione **Azure Ative Directory**.

3. Selecione **Aplicações Empresariais,** em **seguida, todas as aplicações**.

4. **Selecione Adicionar uma aplicação**e, em seguida, selecione a categoria **All.**

5. Procure **workday Writeback**e adicione essa aplicação na galeria.

6. Depois da adição da aplicação e do ecrã de detalhes da aplicação, selecione **Provisioning**.

7. Mude o **modo** **de provisionamento** para **automático**.

8. Complete a secção **de Credenciais de Administrador** da seguinte forma:

   * **Nome** de utilizador do Administrador – Introduza o nome de utilizador da conta do sistema de integração Workday, com o nome de domínio do arrendatário anexado. Deve parecer algo como: *username\@contoso4*

   * **Senha de administração –** Insira a palavra-passe da conta do sistema de integração do Dia de Trabalho

   * **URL do inquilino –** Insira o URL no ponto final dos serviços web workday para o seu inquilino. Este valor deve https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resourcesparecer: , onde o *contoso4* é substituído pelo seu nome de inquilino correto e *wd3-impl* é substituído pela corda ambiental correta (se necessário).

   * **Email de notificação –** Insira o seu endereço de e-mail e verifique a caixa de verificação "enviar e-mail se ocorrer falha".

   * Clique no botão **de ligação** de teste. Se o teste de ligação for bem sucedido, clique no botão **Guardar** na parte superior. Se falhar, verifique duas vezes se o URL do Dia de Trabalho e as credenciais são válidas no Dia do Trabalho.

### <a name="part-2-configure-writeback-attribute-mappings"></a>Parte 2: Configurar mapeamentos de atributos de redação

Nesta secção, irá configurar como os atributos de redação fluem de Azure AD para Workday. Atualmente, o conector apenas suporta a redação do endereço de e-mail e do nome de utilizador para o Dia de Trabalho.

1. No separador provisionamento em **Mapeamentos,** clique em **Synchronize Azure Ative Directory Users to Workday**.

2. No campo **Source Object Scope,** pode filtrar opcionalmente, que conjuntos de utilizadores no Diretório Ativo do Azure devem ter os seus endereços de e-mail reescritos no Workday. O âmbito padrão é "todos os utilizadores em Azure AD".

3. Na secção de **mapeamento** sacar, atualize o ID correspondente para indicar o atributo no Diretório Ativo Azure onde o ID do trabalhador do Workday ou identificação do empregado é armazenado. Um método de correspondência popular é sincronizar o ID ou id do trabalhador do trabalhador do workday para extensãoAttribute1-15 em Azure AD, e, em seguida, usar este atributo em Azure AD para combinar os utilizadores no Dia de Trabalho.

4. Normalmente, mapeia o atributo do utilizador Azure AD *PrincipalName* ao atributo do *UserID* do Dia de Trabalho e mapeie o atributo de *correio* AD Azure ao atributo do Workday *EmailAddress.* Para guardar os seus mapeamentos, clique em **Guardar** no topo da secção Attribute-Mapping.

Uma vez concluída a configuração de mapeamento do atributo, pode agora [ativar e lançar o serviço](#enable-and-launch-user-provisioning)de fornecimento do utilizador .

## <a name="enable-and-launch-user-provisioning"></a>Ativar e lançar o fornecimento de utilizadores

Uma vez concluídas as configurações da aplicação de provisionamento Workday, pode ligar o serviço de provisionamento no portal Azure.

> [!TIP]
> Por predefinição, quando ligar o serviço de provisionamento, iniciará operações de provisionamento para todos os utilizadores no âmbito. Se houver erros nas questões de mapeamento ou dados do Dia do Trabalho, então o trabalho de provisionamento pode falhar e ir para o estado de quarentena. Para evitar isto, como uma boa prática, recomendamos configurar o filtro **Source Object Scope** e testar os seus mapeamentos de atributos com alguns utilizadores de teste antes de lançar a sincronização completa para todos os utilizadores. Uma vez verificado que os mapeamentos funcionam e estão a dar-lhe os resultados desejados, então pode remover o filtro ou expandi-lo gradualmente para incluir mais utilizadores.

1. No separador **provisionamento,** coloque o estado de **provisionamento** **ligado**.

2. Clique em **Guardar**.

3. Esta operação iniciará a sincronização inicial, que pode demorar um número variável de horas dependendo de quantos utilizadores estão no inquilino do Dia do Trabalho. 

4. A qualquer momento, verifique o separador de **registos de auditoria** no portal Azure para ver que ações o serviço de provisionamento realizou. Os registos de auditoria listam todos os eventos de sincronização individuais realizados pelo serviço de provisionamento, tais como os utilizadores que estão a ser lidos fora do Workday e, posteriormente, adicionados ou atualizados para o Ative Directory. Consulte a secção de resolução de problemas para obter instruções sobre como rever os registos de auditoria e corrigir erros de provisionamento.

5. Uma vez concluída a sincronização inicial, escreverá um relatório resumo de auditoria no separador **Provisioning,** como mostrado abaixo.

   ![Portal do Azure](./media/workday-inbound-tutorial/wd_3.png)

## <a name="frequently-asked-questions-faq"></a>Perguntas Frequentes (FAQ)

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

### <a name="solution-capability-questions"></a>Questões de capacidade de solução

#### <a name="when-processing-a-new-hire-from-workday-how-does-the-solution-set-the-password-for-the-new-user-account-in-active-directory"></a>Ao processar uma nova contratação a partir do Workday, como é que a solução define a palavra-passe para a nova conta de utilizador no Diretório Ativo?

Quando o agente de provisionamento no local recebe um pedido para criar uma nova conta AD, gera automaticamente uma senha aleatória complexa projetada para satisfazer os requisitos de complexidade da palavra-passe definidos pelo servidor AD e define-a no objeto do utilizador. Esta senha não está registada em lado nenhum.

#### <a name="does-the-solution-support-sending-email-notifications-after-provisioning-operations-complete"></a>A solução suporta o envio de notificações por e-mail após a conclusão das operações de provisionamento?

Não, o envio de notificações por e-mail após o preenchimento das operações de provisionamento não é suportado na versão atual.

#### <a name="how-do-i-manage-delivery-of-passwords-for-new-hires-and-securely-provide-a-mechanism-to-reset-their-password"></a>Como posso gerir a entrega de senhas para novas contratações e fornecer de forma segura um mecanismo para redefinir a sua palavra-passe?

Um dos passos finais envolvidos no fornecimento de novas contas AD é a entrega da senha temporária atribuída à conta AD do utilizador. Muitas empresas ainda usam a abordagem tradicional de entregar a senha temporária ao gestor do utilizador, que depois entrega a senha ao novo trabalhador contratado/contingente. Este processo tem uma falha de segurança inerente e existe uma opção disponível para implementar uma melhor abordagem usando as capacidades da AD Azure.

Como parte do processo de contratação, as equipas de RH geralmente fazem uma verificação de antecedentes e examinam o número de telemóvel da nova contratação. Com a integração do Workday to AD User Provisioning, pode basear-se neste facto e lançar uma capacidade de redefinição de senha de autosserviço para o utilizador no Dia 1. Isto é conseguido através da propagação do atributo "Número Móvel" do novo aluguer do Workday para a AD e, em seguida, de AD para Azure AD utilizando o Azure AD Connect. Uma vez que o "Número Móvel" esteja presente no Azure AD, pode ativar o Reset de [Palavra-Passe self-service (SSPR)](../authentication/howto-sspr-authenticationdata.md) para a conta do utilizador, de modo a que, no dia 1, um novo aluguer possa utilizar o número de telemóvel registado e verificado para autenticação.

#### <a name="does-the-solution-cache-workday-user-profiles-in-the-azure-ad-cloud-or-at-the-provisioning-agent-layer"></a>A solução cache Perfis de utilizador do Dia de Trabalho na nuvem ad ad azure ou na camada do agente de provisionamento?

Não, a solução não mantém uma cache de perfis de utilizador. O serviço de provisionamento de Anúncios Azure funciona simplesmente como um processador de dados, lendo dados do Workday e escrevendo para o diretório ativo ou Azure AD. Consulte a secção Gestão de [dados pessoais](#managing-personal-data) para obter detalhes relacionados com a privacidade do utilizador e a retenção de dados.

#### <a name="does-the-solution-support-assigning-on-premises-ad-groups-to-the-user"></a>A solução suporta a atribuição de grupos aD no local ao utilizador?

Esta funcionalidade não é suportada atualmente. A supressão recomendada é implementar um script PowerShell que questione o ponto final da Microsoft Graph API para [obter dados](https://docs.microsoft.com/graph/api/resources/azure-ad-auditlog-overview?view=graph-rest-beta) de registo de auditoria e usá-lo para desencadear cenários como a atribuição de grupo. Este script PowerShell pode ser anexado a um programador de tarefas e implantado na mesma caixa que executa o agente de provisionamento.  

#### <a name="which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles"></a>Que APIs workday utiliza a solução para consultar e atualizar perfis de trabalhadores do Workday?

A solução utiliza atualmente as seguintes APIs do Dia do Trabalho:

* O formato **URL da API da Workday Web Services** usado na secção **Credenciais de Administrador,** determina a versão API utilizada para Get_Workers
  * Se o formato\#\#\#\#\.URL\.for: https:// dia de trabalho com/ccx/service/tenantName, então a API v21.1 é utilizada. 
  * Se o formato\#\#\#\#\.URL\.for: https:// dia de trabalho\_com/ccx/service/tenantName/Recursos Humanos, então a API v21.1 é utilizada 
  * Se o formato\#\#\#\#\.URL\.for: https:// dia de trabalho\_com/ccx/service/tenantName/Recursos Humanos/v,\# \# \. \# então a versão API especificada é utilizada. (Exemplo: se o v34.0 for especificado, então é utilizado.)  
   
* A funcionalidade Workday Email Writeback utiliza Change_Work_Contact_Information (v30.0) 
* A função Workday Username Writeback utiliza Update_Workday_Account (v31.2) 

#### <a name="can-i-configure-my-workday-hcm-tenant-with-two-azure-ad-tenants"></a>Posso configurar o meu inquilino HCM do Workday com dois inquilinos da AD Azure?

Sim, esta configuração é suportada. Aqui estão os passos de alto nível para configurar este cenário:

* Implementar o agente de provisionamento #1 e registá-lo com #1 de inquilino da AD Azure.
* Implementar o agente de provisionamento #2 e registá-lo com o inquilino da AD Azure #2.
* Com base nos "Domínios infantis" que cada Agente de Provisionamento irá gerir, configurar cada agente com o ou os domínios. Um agente pode lidar com vários domínios.
* No portal Azure, configure a App de Fornecimento de Utilizadores AD em cada inquilino e configure-a com os respetivos domínios.

#### <a name="why-workday-to-azure-ad-user-provisioning-app-is-not-supported-if-we-have-deployed-azure-ad-connect"></a>Por que razão a aplicação de fornecimento de utilizadores "Workday to Azure AD" não é suportada se implementámos o Azure AD Connect?

Quando o Azure AD é utilizado em modo híbrido (onde contém uma mistura de utilizadores de nuvem + no local), é importante ter uma definição clara de "fonte de autoridade". Cenários tipicamente híbridos requerem a implantação do Azure AD Connect. Quando o Azure AD Connect é implantado, o AD no local é a fonte de autoridade. Introduzir o conector AD Azure na mistura pode levar a uma situação em que os valores de atribuição do Workday podem potencialmente sobrepor os valores definidos pelo Azure AD Connect. Por conseguinte, a utilização da aplicação de provisionamento "Workday to Azure AD" não é suportada quando o Azure AD Connect estiver ativado. Nestas situações, recomendamos a utilização de uma aplicação de provisionamento "Workday to AD User" para colocar os utilizadores no local a d.A. e, em seguida, sincronizá-los em Azure AD Connect usando o Azure AD Connect.

#### <a name="how-do-i-suggest-improvements-or-request-new-features-related-to-workday-and-azure-ad-integration"></a>Como posso sugerir melhorias ou solicitar novas funcionalidades relacionadas com a integração da AD Workday e Azure?

O seu feedback é altamente valorizado, pois ajuda-nos a definir a direção para os futuros lançamentos e melhorias. Congratulamo-nos com todo o feedback e encorajamo-lo a submeter a sua ideia ou sugestão de melhoria no [fórum de feedback da Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory). Para obter feedback específico relacionado com a integração do Dia de Trabalho, selecione a categoria *Aplicações SaaS* e procure usando as palavras-chave *Workday* para encontrar feedback existente relacionado com o Dia de Trabalho.

![Aplicações UserVoice SaaS](media/workday-inbound-tutorial/uservoice_saas_apps.png)

![Dia de trabalho da UserVoice](media/workday-inbound-tutorial/uservoice_workday_feedback.png)

Ao sugerir uma nova ideia, verifique se alguém já sugeriu uma funcionalidade semelhante. Nesse caso, pode votar o pedido de apresentação ou reforço. Também pode deixar um comentário sobre o seu caso de uso específico para mostrar o seu apoio à ideia e demonstrar como a funcionalidade também será valiosa para si.

### <a name="provisioning-agent-questions"></a>Perguntas do Agente de Provisionamento

#### <a name="what-is-the-ga-version-of-the-provisioning-agent"></a>Qual é a versão GA do Agente de Provisionamento?

* A versão GA do Agente de Provisionamento é 1.1.30 ou superior.
* Se a versão do seu agente for inferior a 1.1.30, está a executar a versão de pré-visualização pública e será automaticamente atualizada para a versão GA se o servidor que hospeda o agente tiver um tempo de execução .NET 4.7.1.
  * Pode [verificar a versão .NET](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) instalada no seu servidor. Se o servidor não estiver a funcionar .NET 4.7.1, pode [descarregar e instalar .NET 4.7.1](https://support.microsoft.com/help/4033342/the-net-framework-4-7-1-offline-installer-for-windows). O seu agente de fornecimento será automaticamente atualizado para a versão GA depois de instalar .NET 4.7.1.

#### <a name="how-do-i-know-the-version-of-my-provisioning-agent"></a>Como conheço a versão do meu Agente de Provisionamento?

* Inscreva-se no servidor do Windows onde o Agente de Provisionamento está instalado.
* Vá ao **Painel** -> de Controlo**Desinstalar ou alterar um** menu de programa
* Procure a versão correspondente à entrada **Microsoft Azure AD Connect Provisioning Agent**

  ![Portal do Azure](./media/workday-inbound-tutorial/pa_version.png)

#### <a name="does-microsoft-automatically-push-provisioning-agent-updates"></a>A Microsoft pressiona automaticamente as atualizações do Agente de Provisionamento?

Sim, a Microsoft atualiza automaticamente o agente de provisionamento. Pode desativar atualizações automáticas ao parar o atualização do agente de ligação ad do Windows **Microsoft Azure AD**.

#### <a name="can-i-install-the-provisioning-agent-on-the-same-server-running-azure-ad-connect"></a>Posso instalar o Agente de Provisionamento no mesmo servidor que executa o Azure AD Connect?

Sim, pode instalar o Agente de Provisionamento no mesmo servidor que executa o Azure AD Connect.

#### <a name="at-the-time-of-configuration-the-provisioning-agent-prompts-for-azure-ad-admin-credentials-does-the-agent-store-the-credentials-locally-on-the-server"></a>No momento da configuração, o Agente de Provisionamento solicita credenciais de administração da AD Azure. O Agente armazena as credenciais localmente no servidor?

Durante a configuração, o Agente de Provisionamento solicita que as credenciais de administração da AD Azure se conectem ao seu inquilino Azure AD. Não armazena as credenciais localmente no servidor. No entanto, mantém as credenciais utilizadas para se ligar ao *domínio ative diretório no local* num cofre de senha seletiva do Windows local.

#### <a name="how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication"></a>Como configurar o Agente de Provisionamento para utilizar um servidor proxy para comunicação HTTP de saída?

O Agente de Provisionamento apoia a utilização de procuração de saída. Pode configurá-lo editando o ficheiro **c:\Program Files\Microsoft Azure AD Connect Provisioning Agent\AADConnectProvisioningAgent.exe.config**. Adicione as seguintes linhas, na extremidade do ficheiro, `</configuration>` pouco antes da etiqueta de fecho.
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

#### <a name="how-do-i-ensure-that-the-provisioning-agent-is-able-to-communicate-with-the-azure-ad-tenant-and-no-firewalls-are-blocking-ports-required-by-the-agent"></a>Como posso garantir que o Agente de Provisionamento é capaz de comunicar com o inquilino da AD Azure e que não há firewalls bloqueando portas exigidas pelo agente?

Também pode verificar se tem todas as portas necessárias abertas abrindo a ferramenta de teste de portas de [conector](https://aadap-portcheck.connectorporttest.msappproxy.net/) a partir da sua rede de instalações. Mais marcas de verificação verdes significa maior resiliência.

Para se certificar de que a ferramenta lhe dá os resultados certos, certifique-se de:

* Abra a ferramenta num browser a partir do servidor onde instalou o Agente de Provisionamento.
* Certifique-se de que quaisquer proxies ou firewalls aplicáveis ao seu Agente de Provisionamento também são aplicados a esta página. Isto pode ser feito no Internet Explorer indo para **Definições -> Opções de Internet - ligações > - > Definições LAN**. Nesta página, vê o campo "Use um Servidor proxy para o seu LAN". Selecione esta caixa e coloque o endereço proxy no campo "Endereço".

#### <a name="can-one-provisioning-agent-be-configured-to-provision-multiple-ad-domains"></a>Um agente de provisionamento pode ser configurado para fornecer vários domínios ad?

Sim, um agente de provisionamento pode ser configurado para lidar com vários domínios ad, desde que o agente tenha uma linha de visão para os respetivos controladores de domínio. A Microsoft recomenda a criação de um grupo de 3 agentes de provisionamento que servem o mesmo conjunto de domínios adpara garantir uma elevada disponibilidade e fornecer falhas no suporte.

#### <a name="how-do-i-de-register-the-domain-associated-with-my-provisioning-agent"></a>Como posso desregistar o domínio associado ao meu Agente de Provisionamento?

* Do portal Azure, obtenha a *identificação* do inquilino do seu inquilino Azure AD.
* Inscreva-se no servidor do Windows que executa o Agente de Provisionamento.
* Abra powerShell como Administrador do Windows.
* Mude para o diretório que contenha os scripts \[de\] inscrição e execute os seguintes comandos substituindo o parâmetro de identificação do inquilino pelo valor do seu ID de inquilino.

  ```powershell
  cd “C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\RegistrationPowershell\Modules\PSModulesFolder”
  Import-Module "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\RegistrationPowershell\Modules\PSModulesFolder\AppProxyPSModule.psd1"
  Get-PublishedResources -TenantId "[tenant ID]"
  ```

* A partir da lista de agentes que `id` aparecem – copie o valor do campo a partir desse recurso cujo nome de *recurso* é igual ao seu nome de domínio AD.
* Colar o valor de identificação neste comando e executar o comando em PowerShell.

  ```powershell
  Remove-PublishedResource -ResourceId "[resource ID]" -TenantId "[tenant ID]"
  ```

* Reexecutar o assistente de configuração do Agente.
* Quaisquer outros agentes que tenham sido previamente atribuídos a este domínio terão de ser reconfigurados.

#### <a name="how-do-i-uninstall-the-provisioning-agent"></a>Como desinstalar o Agente de Provisionamento?

* Inscreva-se no servidor do Windows onde o Agente de Provisionamento está instalado.
* Vá ao **Painel** -> de Controlo**Desinstalar ou alterar um** menu de programa
* Desinstale os seguintes programas:
  * Agente de provisionamento de ligação ad da Microsoft Azure
  * Atualização do agente de ligação ad da Microsoft Azure
  * Pacote de agente de provisionamento de ligação ad da Microsoft Azure

### <a name="workday-to-ad-attribute-mapping-and-configuration-questions"></a>Dia de trabalho a atributo sacar questões de mapeamento e configuração

#### <a name="how-do-i-back-up-or-export-a-working-copy-of-my-workday-provisioning-attribute-mapping-and-schema"></a>Como posso fazer o apoio ou exportar uma cópia de trabalho do meu Workday Provisioning Attribute Mapping e Schema?

Pode utilizar a Microsoft Graph API para exportar a sua configuração de fornecimento de utilizadores do Dia de Trabalho. Consulte os passos da secção Exportação e Importação da configuração de mapeamento de [atributos](#exporting-and-importing-your-configuration) de fornecimento de utilizadores de trabalho para mais detalhes.

#### <a name="i-have-custom-attributes-in-workday-and-active-directory-how-do-i-configure-the-solution-to-work-with-my-custom-attributes"></a>Tenho atributos personalizados no Workday e no Ative Directy. Como configurar a solução para trabalhar com os meus atributos personalizados?

A solução suporta atributos personalizados do Workday e do Ative Directory. Para adicionar os seus atributos personalizados ao esquema de mapeamento, abra a lâmina de **Mapeamento do Atributo** e desça para expandir a secção **Mostrar opções avançadas**. 

![Editar lista de atributos](./media/workday-inbound-tutorial/wd_edit_attr_list.png)

Para adicionar os seus atributos personalizados do Workday, selecione a lista de *atributos* editar a opção para o Workday e adicionar os seus atributos AD personalizados, selecione a lista de atributos editar opção *para On Premises Ative Directory*.

Veja também:

* [Personalizando a lista de atributos do utilizador do Dia do Trabalho](#customizing-the-list-of-workday-user-attributes)

#### <a name="how-do-i-configure-the-solution-to-only-update-attributes-in-ad-based-on-workday-changes-and-not-create-any-new-ad-accounts"></a>Como configurar a solução para apenas atualizar atributos em AD com base em alterações do Workday e não criar novas contas de AD?

Esta configuração pode ser conseguida definindo as Ações de **Objecto-Alvo** na lâmina de Mapeamento de **Atributos,** como mostrado abaixo:

![Atualizar ação](./media/workday-inbound-tutorial/wd_target_update_only.png)

Selecione a caixa de verificação "Update" para apenas atualizar operações para fluir do Workday para a AD. 

#### <a name="can-i-provision-users-photo-from-workday-to-active-directory"></a>Posso fornecer a foto do utilizador do Workday ao Ative Directory?

A tualmente, a solução não suporta a definição de atributos binários, como *miniaturaSPhoto* e *jpegPhoto* em Diretório Ativo.

#### <a name="how-do-i-sync-mobile-numbers-from-workday-based-on-user-consent-for-public-usage"></a>Como posso sincronizar os números de telemóvel do Workday com base no consentimento do utilizador para uso público?

* Vá a lâmina "Provisioning" da sua App de Provisionamento do Dia de Trabalho.
* Clique nos Mapeamentos de Atributos 
* Em **Mapeamentos**, **selecione Synchronize Workday Workers to On Premises Ative Directory** (ou **Synchronize Workday Workers to Azure AD**).
* Na página de Mappings do Atributo, desloque-se e verifique a caixa "Mostrar Opções Avançadas".  Clique na lista de **atributos editar para o Dia de Trabalho**
* Na lâmina que se abre, localize o atributo "Mobile" e clique na linha para que possa editar o RGPD Móvel de **Expressão** ![API](./media/workday-inbound-tutorial/mobile_gdpr.png)

* Substitua a **Expressão API** pela seguinte nova expressão, que recupera o número móvel de trabalho apenas se a "Bandeira de Uso Público" estiver definida como "Verdadeira" no Dia do Trabalho.

    ```
     wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Contact_Data/wd:Phone_Data[translate(string(wd:Phone_Device_Type_Reference/@wd:Descriptor),'abcdefghijklmnopqrstuvwxyz','ABCDEFGHIJKLMNOPQRSTUVWXYZ')='MOBILE' and translate(string(wd:Usage_Data/wd:Type_Data/wd:Type_Reference/@wd:Descriptor),'abcdefghijklmnopqrstuvwxyz','ABCDEFGHIJKLMNOPQRSTUVWXYZ')='WORK' and string(wd:Usage_Data/@wd:Public)='1']/@wd:Formatted_Phone
    ```

* Salve a Lista de Atributos.
* Guarde o Mapeamento do Atributo.
* Limpe o estado atual e reinicie a sincronização completa.

#### <a name="how-do-i-format-display-names-in-ad-based-on-the-users-departmentcountrycity-attributes-and-handle-regional-variances"></a>Como posso verpuleto nomes em AD com base nos atributos do departamento/país/cidade do utilizador e lidar com variações regionais?

É um requisito comum configurar o atributo de nome de *exibição* em AD de modo a que também forneça informações sobre o departamento do utilizador e país/região. Por exemplo, se John Smith trabalhar no Departamento de Marketing dos EUA, talvez queira que o seu nome de *exibição* apareça como *Smith, John (Marketing-US)*.

Aqui está como você pode lidar com tais requisitos para construir *CN* ou *displayName* para incluir atributos como empresa, unidade de negócio, cidade ou país/região.

* Cada atributo do Dia de Trabalho é recuperado usando uma expressão API XPATH subjacente, que é configurável em **Attribute Mapping -> Lista avançada de atributos**de Edição > Para o Dia de Trabalho . Aqui está a expressão padrão XPATH API para o Workday *PreferredFirstName*, *PreferredLastName*, *Company* and *SupervisoryOrganization* atribui.

     | Atributo de dia de trabalho | Expressão API XPATH |
     | ----------------- | -------------------- |
     | Nome preferido | wd:Trabalhador/wd:Worker_Data/wd:Personal_Data/wd:Name_Data/wd:Preferred_Name_Data/wd:Name_Detail_Data/wd:First_Name/text() |
     | Nome PreferidoLastName | wd:Trabalhador/wd:Worker_Data/wd:Personal_Data/wd:Name_Data/wd:Preferred_Name_Data/wd:Name_Detail_Data/wd:Last_Name/text() |
     | Empresa | wd:Trabalhador/wd:Worker_Data/wd:Organization_Data/wd:Worker_Organization_Data[wd:Organization_Data/wd:Organization_Type_Reference/wd:ID[@wd:type='Organization_Type_ID'='Empresa']/wd:Organization_Reference/@wd:Descriptor |
     | Organização de Supervisão | wd:Worker/wd:Worker_Data/wd:Organization_Data/wd:Worker_Organization_Data/wd:Organization_Data[wd:Organization_Type_Reference/wd:ID[@wd:type='Organization_Type_ID'='Supervisor']/wd:Organization_Name/text() |
  
   Confirme com a sua equipa workday que a expressão API acima é válida para a configuração do seu inquilino workday. Se necessário, pode editá-los conforme descrito na secção [Personalizando a lista de atributos do utilizador do Dia](#customizing-the-list-of-workday-user-attributes)de Trabalho .

* Da mesma forma, as informações do país presentes no Dia de Trabalho são recuperadas utilizando o seguinte XPATH: *wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference*

     Existem 5 atributos relacionados com o país que estão disponíveis na secção de lista de atributos do Dia de Trabalho.

     | Atributo de dia de trabalho | Expressão API XPATH |
     | ----------------- | -------------------- |
     | Referência do país | wd:Trabalhador/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/wd:ID@wd:type[ ='ISO_3166-1_Alpha-3_Code']/text() |
     | CountryReferenceFriendly | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/@wd:Descriptor |
     | CountryReferenceNumeric | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/wd:ID@wd:type[ ='ISO_3166-1_Numeric-3_Code']/text() |
     | PaísReferênciaTwoLetter | wd:Trabalhador/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/wd:ID@wd:type[ ='ISO_3166-1_Alpha-2_Code']/text() |
     | Referência countryregion | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Region_Reference/@wd:Descriptor |

  Confirme com a sua equipa workday que as expressões API acima são válidas para a configuração do seu inquilino workday. Se necessário, pode editá-los conforme descrito na secção [Personalizando a lista de atributos do utilizador do Dia](#customizing-the-list-of-workday-user-attributes)de Trabalho .

* Para construir a expressão de mapeamento certo, identifique qual o atributo do Workday "de forma autoritária" representa o primeiro nome, apelido, país/região e departamento do utilizador. Digamos que os atributos são *PreferredFirstName*, *PreferredLastName*, *CountryReferenceTwoLetter* e *SupervisoryOrganization,* respectivamente. Pode usá-lo para construir uma expressão para o atributo ad nome de *exibição* DaD da seguinte forma para obter um nome de exibição como *Smith, John (Marketing-US)*.

    ```
     Append(Join(", ",[PreferredLastName],[PreferredFirstName]), Join(""," (",[SupervisoryOrganization],"-",[CountryReferenceTwoLetter],")"))
    ```
    Uma vez que tenha a expressão certa, edite a tabela de Mapeamentos ![de Atributoe e modifique o mapeamento do atributo do nome do *ecrã* como mostrado abaixo: DisplayName Mapping](./media/workday-inbound-tutorial/wd_displayname_map.png)

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

#### <a name="how-can-i-use-selectuniquevalue-to-generate-unique-values-for-samaccountname-attribute"></a>Como posso usar selectUniqueValue para gerar valores únicos para atributo samAccountName?

Digamos que pretende gerar valores únicos para atributo *samAccountName* usando uma combinação de atributos *FirstName* e *LastName* do Workday. Dada abaixo está uma expressão com a que pode começar:

```
SelectUniqueValue(
    Replace(Mid(Replace(NormalizeDiacritics(StripSpaces(Join("",  Mid([FirstName],1,1), [LastName]))), , "([\\/\\\\\\[\\]\\:\\;\\|\\=\\,\\+\\*\\?\\<\\>])", , "", , ), 1, 20), , "(\\.)*$", , "", , ),
    Replace(Mid(Replace(NormalizeDiacritics(StripSpaces(Join("",  Mid([FirstName],1,2), [LastName]))), , "([\\/\\\\\\[\\]\\:\\;\\|\\=\\,\\+\\*\\?\\<\\>])", , "", , ), 1, 20), , "(\\.)*$", , "", , ),
    Replace(Mid(Replace(NormalizeDiacritics(StripSpaces(Join("",  Mid([FirstName],1,3), [LastName]))), , "([\\/\\\\\\[\\]\\:\\;\\|\\=\\,\\+\\*\\?\\<\\>])", , "", , ), 1, 20), , "(\\.)*$", , "", , )
)
```

Como funciona a expressão acima: Se o utilizador é John Smith, primeiro tenta gerar JSmith, se JSmith já existe, então gera JoSmith, se isso existe, gera JohSmith. A expressão também garante que o valor gerado satisfaz a restrição de comprimento e a restrição de caracteres especiais associada ao *samAccountName*.

Veja também:

* [Sintaxe de Função Média](../app-provisioning/functions-for-customizing-application-data.md#mid)
* [Substituir a Sintaxe de Função](../app-provisioning/functions-for-customizing-application-data.md#replace)
* [SelectUniqueValue Function Syntax](../app-provisioning/functions-for-customizing-application-data.md#selectuniquevalue)

#### <a name="how-do-i-remove-characters-with-diacritics-and-convert-them-into-normal-english-alphabets"></a>Como posso remover caracteres com diacritics e convertê-los em alfabetos ingleses normais?

Utilize a função [NormalizeDiacritics](../app-provisioning/functions-for-customizing-application-data.md#normalizediacritics) para remover caracteres especiais em primeiro nome e apelido do utilizador, enquanto constrói o endereço de e-mail ou o valor NC para o utilizador.

## <a name="troubleshooting-tips"></a>Sugestões de resolução de problemas

Esta secção fornece orientações específicas sobre como resolver problemas de fornecimento com a sua integração no Dia de Trabalho utilizando os registos de auditoria da AD Azure e os registos do Espectador de Eventos do Windows Server. Baseia-se em cima dos passos genéricos de resolução de problemas e conceitos capturados no [Tutorial: Reportagem sobre o fornecimento automático](../app-provisioning/check-status-user-account-provisioning.md) de conta de utilizador

Esta secção abrange os seguintes aspetos de resolução de problemas:

* [Configuração do Espectador de Eventos do Windows para resolução de problemas do agente](#setting-up-windows-event-viewer-for-agent-troubleshooting)
* [Criação de registos de auditoria do portal Azure para resolução de problemas de serviço](#setting-up-azure-portal-audit-logs-for-service-troubleshooting)
* [A compreensão dos registos para a Conta de Utilizador AD cria operações](#understanding-logs-for-ad-user-account-create-operations)
* [Compreensão de registos para operações de atualização do Gestor](#understanding-logs-for-manager-update-operations)
* [Resolução de erros comummente encontrados](#resolving-commonly-encountered-errors)

### <a name="setting-up-windows-event-viewer-for-agent-troubleshooting"></a>Configuração do Espectador de Eventos do Windows para resolução de problemas do agente

* Inscreva-se na máquina do Servidor do Windows onde o Agente de Provisionamento está implantado
* Abra a aplicação de ambiente de trabalho do **Espectador do Evento do Windows Server.**
* Selecione **Registos windows > Aplicação**.
* Utilize o registo de corrente de **filtro...** opção de ver todos os eventos registados sob a fonte **AAD. Connect.ProvisioningAgent** e excluir eventos com ID do evento "5", especificando o filtro "-5" como mostrado abaixo.

  ![Espectador de eventos do Windows](media/workday-inbound-tutorial/wd_event_viewer_01.png))

* Clique em **OK** e separe a visualização do resultado por parte da coluna **Date and Time.**

### <a name="setting-up-azure-portal-audit-logs-for-service-troubleshooting"></a>Criação de registos de auditoria do portal Azure para resolução de problemas de serviço

* Lance o [portal Azure](https://portal.azure.com)e navegue para a secção de **registos** de Auditoria da sua aplicação de provisionamento do Dia de Trabalho.
* Utilize o botão **Colunas** na página Registos de Auditoria para visualizar apenas as seguintes colunas na vista (Data, Atividade, Estado, Razão de Estado). Esta configuração garante que se foca apenas em dados relevantes para a resolução de problemas.

  ![Colunas de registo de auditoria](media/workday-inbound-tutorial/wd_audit_logs_00.png)

* Utilize os parâmetros de consulta **de Destino** e **Data Range** para filtrar a vista. 
  * Defina o parâmetro de consulta **do target** para o "ID do trabalhador" ou "IDENTIFICAÇÃO do empregado" do objeto de trabalhador do Dia de Trabalho.
  * Detete o Intervalo de **Data** para um período de tempo adequado durante o qual pretende investigar por erros ou problemas com o provisionamento.

  ![Filtros de registo de auditoria](media/workday-inbound-tutorial/wd_audit_logs_01.png)

### <a name="understanding-logs-for-ad-user-account-create-operations"></a>A compreensão dos registos para a Conta de Utilizador AD cria operações

Quando é detetada uma nova contratação no Workday (digamos com o Id *21023*do Empregado), o serviço de prestação de serviços da Azure AD tenta criar uma nova conta de utilizador aD para o trabalhador e no processo cria 4 registos de registos de auditoria conforme descrito abaixo:

  [![Registo de auditoria cria operações](media/workday-inbound-tutorial/wd_audit_logs_02.png)](media/workday-inbound-tutorial/wd_audit_logs_02.png#lightbox)

Quando clica em qualquer um dos registos de registos de auditoria, a página **Dedetalhes** de Atividade sabre. Aqui está o que a página Detalhes da **Atividade** mostra para cada tipo de registo de registo.

* Registo de **importação de dia de trabalho:** Este registo de registo mostra as informações dos trabalhadores recolhidas no Dia de Trabalho. Utilize informações na secção *Detalhes Adicionais* do registo de registo para resolver problemas com a recolha de dados do Dia do Trabalho. Um registo de exemplo é mostrado abaixo juntamente com ponteiros sobre como interpretar cada campo.

  ```JSON
  ErrorCode : None  // Use the error code captured here to troubleshoot Workday issues
  EventName : EntryImportAdd // For full sync, value is "EntryImportAdd" and for delta sync, value is "EntryImport"
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID (usually the Worker ID or Employee ID field)
  SourceAnchor : a071861412de4c2486eb10e5ae0834c3 // set to the WorkdayID (WID) associated with the record
  ```

* Registo de **importação ad:** Este registo de registo mostra informações da conta recolhida a partir de AD. Como durante a criação inicial do utilizador não existe uma conta AD, a *Razão do Estado de Atividade* indicará que nenhuma conta com o valor do atributo de ID correspondente foi encontrada no Diretório Ativo. Utilize informações na secção *Detalhes Adicionais* do registo de registo para resolver problemas com a recolha de dados do Dia do Trabalho. Um registo de exemplo é mostrado abaixo juntamente com ponteiros sobre como interpretar cada campo.

  ```JSON
  ErrorCode : None // Use the error code captured here to troubleshoot Workday issues
  EventName : EntryImportObjectNotFound // Implies that object was not found in AD
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID
  ```

  Para encontrar registos de registos de registos do Agente de Provisionamento correspondentes a esta operação de importação de AD, abra os registos do Windows Event Viewer e utilize o **Find...** opção do menu para encontrar entradas de registo contendo o valor do atributo de identificação/união de propriedade correspondente (neste caso *21023*).

  ![Localizar](media/workday-inbound-tutorial/wd_event_viewer_02.png)

  Procure a entrada com *ID do evento = 9*, que lhe fornecerá o filtro de pesquisa LDAP utilizado pelo agente para recuperar a conta AD. Pode verificar se este é o filtro de pesquisa certo para recuperar entradas únicas do utilizador.

  ![Pesquisa LDAP](media/workday-inbound-tutorial/wd_event_viewer_03.png)

  O registo que o segue imediatamente com ID do *evento = 2* captura o resultado da operação de pesquisa e se devolveu quaisquer resultados.

  ![Resultados do LDAP](media/workday-inbound-tutorial/wd_event_viewer_04.png)

* Registo de ação da regra da **sincronização:** Este registo de registo apresenta os resultados das regras de mapeamento do atributo e filtros de deteção configurados, juntamente com a ação de provisionamento que será tomada para processar o evento de trabalho que se aproxima. Utilize informações na secção *Detalhes Adicionais* do registo de registo saque a problemas com a ação de sincronização. Um registo de exemplo é mostrado abaixo juntamente com ponteiros sobre como interpretar cada campo.

  ```JSON
  ErrorCode : None // Use the error code captured here to troubleshoot sync issues
  EventName : EntrySynchronizationAdd // Implies that the object will be added
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID
  SourceAnchor : a071861412de4c2486eb10e5ae0834c3 // set to the WorkdayID (WID) associated with the profile in Workday
  ```

  Se houver problemas com as expressões de mapeamento do seu atributo ou os dados do Dia de Trabalho que chegam têm problemas (por exemplo: valor vazio ou nulo para atributos necessários), então observará uma falha nesta fase com o ErrorCode fornecendo detalhes da falha.

* **Registo de exportação aD:** Este registo de registo apresenta o resultado da operação de criação de conta AD juntamente com os valores de atributo que foram definidos no processo. Utilize informações na secção *Detalhes Adicionais* do registo de registo para resolver problemas com a operação de criação de conta. Um registo de exemplo é mostrado abaixo juntamente com ponteiros sobre como interpretar cada campo. Na secção "Detalhes Adicionais", o "Nome de Evento" está definido para "EntryExportAdd", o "JoinProperty" está definido para o valor do atributo de ID correspondente, o "SourceAnchor" é definido para o WorkdayID (WID) associado ao registo e o "TargetAnchor" está definido para o valor do atributo AD "ObjectGuid" do utilizador recém-criado. 

  ```JSON
  ErrorCode : None // Use the error code captured here to troubleshoot AD account creation issues
  EventName : EntryExportAdd // Implies that object will be created
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID
  SourceAnchor : a071861412de4c2486eb10e5ae0834c3 // set to the WorkdayID (WID) associated with the profile in Workday
  TargetAnchor : 83f0156c-3222-407e-939c-56677831d525 // set to the value of the AD "objectGuid" attribute of the new user
  ```

  Para encontrar registos de registos de registos do Agente de Provisionamento correspondentes a esta operação de exportação de AD, abra os registos do Windows Event Viewer e utilize o **Find...** opção do menu para encontrar entradas de registo contendo o valor do atributo de identificação/união de propriedade correspondente (neste caso *21023*).  

  Procure um registo HTTP POST correspondente ao carimbo de tempo da operação de exportação com *ID do evento = 2*. Este registo conterá os valores de atributos enviados pelo serviço de provisionamento ao agente de provisionamento.

  [![SCIM Adicionar](media/workday-inbound-tutorial/wd_event_viewer_05.png)](media/workday-inbound-tutorial/wd_event_viewer_05.png#lightbox)

  Imediatamente após o evento acima, deve haver outro evento que capture a resposta da operação de conta AD de criação. Este evento devolve o novo objectuguid criado em AD e é definido como o atributo TargetAnchor no serviço de provisionamento.

  [![SCIM Adicionar](media/workday-inbound-tutorial/wd_event_viewer_06.png)](media/workday-inbound-tutorial/wd_event_viewer_06.png#lightbox)

### <a name="understanding-logs-for-manager-update-operations"></a>Compreender os registos para operações de atualização do gestor

O atributo do gestor é um atributo de referência em AD. O serviço de provisionamento não define o atributo do gestor como parte da operação de criação do utilizador. Em vez disso, o atributo do gestor é definido como parte de uma operação de *atualização* após a criação da conta AD para o utilizador. Expandindo o exemplo acima, digamos que um novo contrato com id "21451" é ativado no Workday e o novo gestor de contratação *(21023)* já tem uma conta AD. Neste cenário, pesquisar os registos da Auditoria para o utilizador 21451 mostra 5 entradas.

  [![Atualização do Gestor](media/workday-inbound-tutorial/wd_audit_logs_03.png)](media/workday-inbound-tutorial/wd_audit_logs_03.png#lightbox)

Os primeiros 4 registos são como os que exploramos como parte da operação de criação do utilizador. O 5º recorde é a exportação associada à atualização do atributo do gestor. O registo de registo mostra o resultado da operação de atualização do gestor de conta AD, que é realizada utilizando o atributo *objectGuid* do gestor.

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

### <a name="resolving-commonly-encountered-errors"></a>Resolução de erros comummente encontrados

Esta secção cobre erros geralmente vistos com o fornecimento de utilizadores do Dia de Trabalho e como resolvê-lo. Os erros são agrupados da seguinte forma:

* [Erros do agente de provisionamento](#provisioning-agent-errors)
* [Erros de conectividade](#connectivity-errors)
* [Erros de criação de conta de utilizador aD](#ad-user-account-creation-errors)
* [Erros de atualização da conta de utilizador aD](#ad-user-account-update-errors)

#### <a name="provisioning-agent-errors"></a>Erros do agente de provisionamento

|#|Cenário de erro |Causas Prováveis|Resolução Recomendada|
|--|---|---|---|
|1.| Erro ao instalar o agente de provisionamento com uma mensagem de erro: *O serviço 'Microsoft Azure AD Connect Provisioning Agent' (AADConnectProvisioningAgent) não começou. Verifique se tem privilégios suficientes para iniciar o sistema.* | Este erro geralmente aparece se estiver a tentar instalar o agente de provisionamento num controlador de domínio e a política de grupo impede que o serviço comece.  Também é visto se tem uma versão anterior do agente em execução e não o desinstalou antes de iniciar uma nova instalação.| Instale o agente de fornecimento num servidor não DC. Certifique-se de que as versões anteriores do agente estão desinstaladas antes de instalar o novo agente.|
|2.| O Serviço Windows 'Microsoft Azure AD Connect Provisioning Agent' *encontra-se* em estado de arranque e não muda para o estado *de execução.* | Como parte da instalação, o assistente do agente cria uma conta local **(NT Service\\AADConnectProvisioningAgent**) no servidor e esta é a conta de logon utilizada para iniciar o serviço. Se uma política de segurança no seu servidor Windows impedir que as contas locais executem os serviços, irá encontrar este erro. | Abra a *consola Services.* Clique no 'Microsoft Azure AD Connect Provisioning Agent' do Serviço Windows e no separador de sessão especifique a conta de um administrador de domínio para executar o serviço. Reinicie o serviço. |
|3.| Ao configurar o agente de provisionamento com o seu domínio AD no passo *Connect Ative Directory,* o assistente demora muito tempo a tentar carregar o esquema AD e, eventualmente, vezes vezes. | Geralmente, este erro aparece se o assistente não conseguir contactar o servidor de controlador de domínio do AD devido a problemas na firewall. | No ecrã do assistente do *Diretório Ativo Connect,* ao mesmo tempo que fornece as credenciais para o seu domínio AD, existe uma opção chamada Prioridade do controlador de *domínio Select*. Utilize esta opção para selecionar um controlador de domínio que se encontre no mesmo site que o servidor do agente e certifique-se de que não existem regras de firewall que bloqueiem a comunicação. |

#### <a name="connectivity-errors"></a>Erros de conectividade

Se o serviço de provisionamento não puder ligar-se ao Workday ou ao Ative Directory, pode fazer com que o fornecimento entre em quarentena. Use a tabela abaixo para resolver problemas de conectividade.

|#|Cenário de erro |Causas Prováveis|Resolução Recomendada|
|--|---|---|---|
|1.| Ao clicar na Ligação de **Teste,** obtém a mensagem de erro: Houve um erro de *ligação ao Diretório Ativo. Certifique-se de que o Agente de Provisionamento no local está em funcionamento e está configurado com o domínio de Diretório Ativo correto.* | Este erro geralmente aparece se o agente de provisionamento não estiver em execução ou se houver uma comunicação de bloqueio de firewall entre a AD Azure e o agente de provisionamento. Também pode ver este erro, se o domínio não estiver configurado no Assistente do Agente. | Abra a consola *Serviços* no servidor Windows para confirmar que o agente está em execução. Abra o assistente do agente de provisionamento e confirme que o domínio certo está registado com o agente.  |
|2.| O trabalho de provisionamento vai para o estado de quarentena durante os fins de semana (Fri-Sat) e recebemos uma notificação por e-mail de que há um erro com a sincronização. | Uma das causas comuns deste erro é o período de indisponibilidade planeado do Workday. Se estiver a utilizar um inquilino de implementação do Workday, tenha em conta que o Workday tem um período de indisponibilidade agendado para os inquilinos de implementação (normalmente, da noite de sexta-feira à manhã de sábado) e, durante esse período, as aplicações de aprovisionamento do Workday podem entrar em quarentena, pois não se conseguem ligar ao Workday. Regressam ao estado normal quando o inquilino de implementação do Workday estiver novamente online. Em casos raros, também poderá ver este erro se a palavra-passe de Utilizador de Sistema de Integração tiver sido alterada devido a uma atualização do inquilino ou se a conta estiver bloqueada ou expirada. | Verifique junto do seu administrador do Workday ou do seu parceiro de integração qual é o período de indisponibilidade do Workday, de modo a ignorar as mensagens de alerta durante esse período e confirmar a disponibilidade assim que a instância do Workday estiver de novo online.  |


#### <a name="ad-user-account-creation-errors"></a>Erros de criação de conta de utilizador aD

|#|Cenário de erro |Causas Prováveis|Resolução Recomendada|
|--|---|---|---|
|1.| Falhas de operação de exportação no registo de auditoria com o erro da *mensagem: OperaçõesError-SvcErr: Ocorreu um erro de operação. Não foi configurada nenhuma referência superior para o serviço de diretório. O serviço de diretório não pode, portanto, emitir referências a objetos fora desta floresta.* | Este erro geralmente aparece se o Recipiente de *Diretório Ativo* OU não for definido corretamente ou se houver problemas com o Mapeamento de Expressão usado para *o nome distinto*dos pais . | Verifique se há tipometro de contentor de *diretório ativo* OU. Se estiver a utilizar *parentDistinguishedName* no mapeamento de atributos, confirme que é sempre avaliado para um contentor conhecido dentro do domínio do AD. Verifique o evento *Export* nos registos de auditoria para ver o valor gerado. |
|2.| Falhas de operação de exportação no registo de auditoria com código de erro: *SystemForCrossDomainIdentityManagementBadResponse* e *error de mensagem: ConstraintViolation-AtrErr: Um valor no pedido é inválido. Um valor para o atributo não estava na gama aceitável de valores. \nErros Detalhes: CONSTRAINT_ATT_TYPE - empresa*. | Embora este erro seja específico do atributo da *empresa,* pode ver este erro para outros atributos como *o CN* também. Este erro aparece devido à restrição de esquema saneada pela AD. Por padrão, os atributos como *a empresa* e a *CN* em AD têm um limite superior de 64 caracteres. Se o valor proveniente do Workday for superior a 64 caracteres, verá esta mensagem de erro. | Verifique o evento *exportação* nos registos de auditoria para ver o valor do atributo relatado na mensagem de erro. Considere truncar o valor proveniente do Dia do Trabalho utilizando a função [Mid](../app-provisioning/functions-for-customizing-application-data.md#mid) ou alterando os mapeamentos para um atributo ad que não tenha restrições de comprimento semelhantes.  |

#### <a name="ad-user-account-update-errors"></a>Erros de atualização da conta de utilizador aD

Durante o processo de atualização da conta de utilizador aD, o serviço de provisionamento lê informações tanto do Workday como da AD, executa as regras de mapeamento do atributo e determina se alguma alteração tem de produzir efeito. Assim, um evento de atualização é desencadeado. Se algum destes passos encontrar uma falha, está registado nos registos de auditoria. Utilize a tabela abaixo para resolver erros comuns de atualização.

|#|Cenário de erro |Causas Prováveis|Resolução Recomendada|
|--|---|---|---|
|1.| Falhas de ação da regra de sincronização no registo de auditoria com o Nome de evento da mensagem *= EntrySynchronizationError and ErrorCode = EndpointInavailable*. | Este erro aparece se o serviço de provisionamento não conseguir obter dados de perfil de utilizador do Ative Directory devido a um erro de processamento encontrado pelo agente de provisionamento no local. | Verifique se o Visualizador de Eventos do Agente de Provisionamento regista os registos de erros que indicam problemas com a operação de leitura (Filter by Event ID #2). |
|2.| O atributo do gestor em AD não é atualizado para certos utilizadores em AD. | A causa mais provável deste erro é se estiver a usar regras de deteção e o gestor do utilizador não fizer parte do âmbito. Também pode encontrar este problema se o atributo de ID correspondente do gestor (por exemplo, EmployeeID) não for encontrado no domínio AD-alvo ou não estiver definido para o valor correto. | Reveja o filtro de deteção e adicione o utilizador gestor no âmbito. Verifique o perfil do gestor em AD para se certificar de que existe um valor para o atributo de IDENTIFICAção correspondente. |

## <a name="managing-your-configuration"></a>Gerir a sua configuração

Esta secção descreve como pode alargar, personalizar e gerir a configuração de fornecimento de utilizadores orientada pelo Workday. Aborda os seguintes tópicos:

* [Personalizando a lista de atributos do utilizador do Dia do Trabalho](#customizing-the-list-of-workday-user-attributes)  
* [Exportar e importar configuração](#exporting-and-importing-your-configuration)

### <a name="customizing-the-list-of-workday-user-attributes"></a>Personalizando a lista de atributos do utilizador do Dia do Trabalho

As aplicações de provisionamento workday para Ative Directory e Azure AD incluem uma lista padrão de atributos de utilizador do Workday que você pode selecionar. No entanto, estas listas não são abrangentes. O Workday suporta muitas centenas de possíveis atributos de utilizador, que podem ser standard ou exclusivos do seu inquilino do Dia do Trabalho.

O serviço de provisionamento de AD Azure suporta a capacidade de personalizar a sua lista ou atributo do Dia de Trabalho para incluir quaisquer atributos expostos na [operação Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) da API de Recursos Humanos.

Para esta alteração, deve utilizar o [Workday Studio](https://community.workday.com/studio-download) para extrair as expressões XPath que representam os atributos que pretende utilizar e, em seguida, adicioná-las à sua configuração de provisionamento utilizando o editor de atributoavançado no portal Azure.

**Para recuperar uma expressão XPath para um atributo do utilizador do Dia de Trabalho:**

1. Descarregue e instale [o Estúdio Workday.](https://community.workday.com/studio-download) Você precisará de uma conta comunitária workday para aceder ao instalador.

2. Descarregue o ficheiro Workday **Human_Resources** WSDL específico da versão WWS API que planeia utilizar a partir do Diretório de [Serviços Web workday](https://community.workday.com/sites/default/files/file-hosting/productionapi/index.html)

3. Estúdio workday de lançamento.

4. A partir da barra de comando, selecione o Workday > Test Web Service na opção **Tester.**

5. Selecione **External**, e selecione o ficheiro WSDL Human_Resources descarregado no passo 2.

    ![Estúdio Workday](./media/workday-inbound-tutorial/wdstudio1.png)

6. Detete o `https://IMPL-CC.workday.com/ccx/service/TENANT/Human_Resources`campo De **Localização** para, mas substituindo "IMPL-CC" pelo seu tipo de instância real, e "TENANT" pelo seu nome real de inquilino.

7. Definir **operação** para **Get_Workers**

8.  Clique no pequeno link **configurado** abaixo das vidraças De Pedido/Resposta para definir as suas credenciais do Dia de Trabalho. Verifique a **Autenticação**e, em seguida, introduza o nome de utilizador e a palavra-passe para a sua conta do sistema de integração workday. Certifique-se de formatar\@o nome de utilizador como inquilino de nome e deixar a opção **UsernameToken de Segurança WS** selecionada.

    ![Estúdio Workday](./media/workday-inbound-tutorial/wdstudio2.png)

9. Selecione **OK**.

10. No painel **Request,** cola no XML abaixo. **Deteto Employee_ID** à identificação do empregado de um verdadeiro utilizador no seu inquilino do Dia do Trabalho. Desloque **wd:versão** para a versão da WWS que pretende utilizar. Selecione um utilizador que tenha o atributo preenchido que deseja extrair.

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

11. Clique no **Pedido de Envio** (seta verde) para executar o comando. Se for bem sucedida, a resposta deve figurar no painel **resposta.** Verifique a resposta para garantir que tem os dados do ID do utilizador que inseriu e não um erro.

12. Se for bem sucedido, copie o XML do painel **resposta** e guarde-o como um ficheiro XML.

13. Na barra de comando do Estúdio Workday, selecione **File > Open File...** e abra o ficheiro XML que guardou. Esta ação abrirá o ficheiro no editor do Workday Studio XML.

    ![Estúdio Workday](./media/workday-inbound-tutorial/wdstudio3.png)

14. Na árvore de arquivo, navegue através de **/env: Envelope > env: Body > wd:Get_Workers_Response > wd:Response_Data > wd: Worker** to find your user's data.

15. Em **wd: Trabalhador,** encontre o atributo que pretende adicionar e selecione-o.

16. Copie a expressão XPath para o seu atributo selecionado fora do campo **Document Path.**

17. Retire o **/env:Envelope/env:Body/wd:Get_Workers_Response/wd:Response_Data/** prefixo da expressão copiada.

18. Se o último item da expressão copiada for um nó (exemplo: "/wd: Birth_Date"), então **anexar/texto()** no final da expressão. Isto não é necessário se o último item/@wd: for um atributo (exemplo: " tipo").

19. O resultado deve `wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()`ser algo como. Este valor é o que irá copiar no portal Azure.

**Para adicionar o seu atributo personalizado do utilizador do Dia de Trabalho à sua configuração de provisionamento:**

1. Lance o [portal Azure](https://portal.azure.com)e navegue até à secção de provisionamento da sua aplicação de provisionamento workday, como descrito anteriormente neste tutorial.

2. Desemposição do estado de **provisionamento** para **desligar**e selecione **Guardar**. Este passo ajudará a garantir que as suas alterações só produzirão efeitos quando estiver pronto.

3. Em **Mapeamentos**, **selecione Synchronize Workday Workers to On Premises Ative Directory** (ou **Synchronize Workday Workers to Azure AD**).

4. Percorra para a parte inferior do próximo ecrã e selecione **Mostrar opções avançadas**.

5. Selecione **editar lista de atributos para O Dia de Trabalho**.

    ![Estúdio Workday](./media/workday-inbound-tutorial/wdstudio_aad1.png)

6. Percorra para o fundo da lista de atributos onde estão os campos de entrada.

7. Para **Nome,** introduza um nome de exibição para o seu atributo.

8. Para **Tipo**, selecione o tipo que corresponde adequadamente ao seu atributo **(a corda** é mais comum).

9. Para **expressão API,** introduza a expressão XPath que copiou do Workday Studio. Exemplo: `wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()`

10. **Selecione Adicionar Atributo**.

    ![Estúdio Workday](./media/workday-inbound-tutorial/wdstudio_aad2.png)

11. Selecione **Guardar** acima e, em seguida, **sim** ao diálogo. Feche o ecrã De Atributo-Mapping se ainda estiver aberto.

12. De volta ao separador principal de **provisionamento,** selecione **Synchronize Workday Workers to On Premises Ative Directory** (ou **Synchronize Workers to Azure AD**) novamente.

13. **Selecione Adicionar novo mapeamento**.

14. O seu novo atributo deve agora figurar na lista de **atributos Fonte.**

15. Adicione um mapeamento para o seu novo atributo como desejado.

16. Quando terminar, lembre-se de voltar a colocar o **Estado de Provisionamento** **em On** e guardar.

### <a name="exporting-and-importing-your-configuration"></a>Exportar e importar configuração

Consulte o artigo Configuração de [fornecimento de exportação e importação](../app-provisioning/export-import-provisioning-configuration.md)

## <a name="managing-personal-data"></a>Gerir dados pessoais

A solução de provisionamento workday para O Diretório Ativo requer que um agente de provisionamento seja instalado num servidor Windows no local, e este agente cria registos no registo do Windows Event que podem conter dados pessoais dependendo do seu Dia de Trabalho para mapeamentos de atributos AD. Para cumprir as obrigações de privacidade do utilizador, pode garantir que nenhum dado é retido nos registos do Evento além das 48 horas, configurando uma tarefa programada pelo Windows para limpar o registo do evento.

O serviço de provisionamento da AD Azure insere-se na categoria de processador de **dados** da classificação rGPD. Como um pipeline de processador de dados, o serviço fornece serviços de processamento de dados a parceiros-chave e consumidores finais. O serviço de provisionamento de AD Azure não gera dados de utilizador e não tem controlo independente sobre os dados pessoais recolhidos e como são utilizados. A recuperação de dados, a gregação, análise e reporte no serviço de provisionamento de AD Azure baseiam-se em dados empresariais existentes.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-hybrid-note.md)]

No que diz respeito à retenção de dados, o serviço de provisionamento de AD Azure não gera relatórios, realiza análises ou fornece insights para além de 30 dias. Por isso, o serviço de provisionamento da Azure AD não armazena, processa ou retém quaisquer dados para além de 30 dias. Este design está em conformidade com os regulamentos do RGPD, os regulamentos de conformidade de privacidade da Microsoft e as políticas de retenção de dados da Azure AD.

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)
* [Saiba como configurar um único sign-on entre o Workday e o Azure Ative Directory](workday-tutorial.md)
* [Saiba como integrar outras aplicações do SaaS com o Diretório Ativo Azure](tutorial-list.md)
* [Saiba como usar APIs do Microsoft Graph para gerir configurações de provisionamento](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)
