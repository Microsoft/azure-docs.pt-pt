---
title: 'Tutorial: Configurar o dia de trabalho para aprovisionamento automático de utilizadores no Azure Active Directory | Documentos da Microsoft'
description: Saiba como configurar o Azure Active Directory para aprovisionar e desaprovisionar contas de utilizador com o Workday.
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
ms.date: 01/19/2019
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: 73e5b081e85726a1fc78d92996846faa18ce616a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57897627"
---
# <a name="tutorial-configure-workday-for-automatic-user-provisioning"></a>Tutorial: Configurar o dia de trabalho para aprovisionamento automático de utilizadores

O objetivo deste tutorial é mostrar as etapas que precisa ser executadas para importar perfis de trabalho a partir do Workday para Active Directory e Azure Active Directory, com repetição de escrita opcional de endereço de e-mail no Workday.

## <a name="overview"></a>Descrição geral

O [utilizador do Azure Active Directory, serviço de aprovisionamento](../manage-apps/user-provisioning.md) integra-se com o [Workday Human Resources API](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) para aprovisionar contas de utilizador. Azure AD utiliza esta ligação para permitir que o utilizador seguinte fluxos de trabalho de aprovisionamento:

* **Aprovisionar utilizadores para o Active Directory** -aprovisionar selecionado conjuntos de utilizadores do Workday para um ou mais domínios do Active Directory.

* **Aprovisionar utilizadores apenas na cloud para o Azure Active Directory** - em cenários onde não for utilizado o Active Directory, os utilizadores no local podem ser aprovisionados diretamente a partir do Workday para o Azure Active Directory com o serviço de aprovisionamento de utilizador do Azure AD.

* **Escrever inverso de endereços de e-mail no Workday** -o serviço de aprovisionamento de utilizador do Azure AD pode gravar os endereços de e-mail dos utilizadores do Azure AD no Workday.

### <a name="what-human-resources-scenarios-does-it-cover"></a>Quais cenários de recursos humanos cobre?

Os Workday utilizador Aprovisiona fluxos de trabalho suportados pelo serviço de aprovisionamento de utilizador do Azure AD ativar a automatização dos seguintes recursos humanos e cenários de gestão do ciclo de vida de identidade:

* **Novos funcionários a contratar** - quando um novo funcionário é adicionado ao Workday, uma conta de utilizador é criada automaticamente no Active Directory, Azure Active Directory e, opcionalmente, do Office 365 e [outras aplicações SaaS suportadas pelo Azure AD](../manage-apps/user-provisioning.md), com repetição de escrita do endereço de e-mail no Workday.

* **Atualizações de atributo e perfil de funcionário** - quando um registro de funcionário é atualizado no Workday (por exemplo, o respetivo nome, título ou gestor), a sua conta de utilizador será atualizada automaticamente no Active Directory, Azure Active Directory e, opcionalmente, do Office 365 e [outras aplicações SaaS suportadas pelo Azure AD](../manage-apps/user-provisioning.md).

* **Términos funcionário** - quando um funcionário é demitido no Workday, a conta de utilizador é automaticamente desativada no Active Directory, Azure Active Directory e, opcionalmente, do Office 365 e [outras aplicações SaaS suportadas pelo Azure AD](../manage-apps/user-provisioning.md).

* **Funcionário rehires** - quando um funcionário é rehired no Workday, sua conta antiga pode ser reativada ou voltar aprovisionada (dependendo da sua preferência) para Active Directory, Azure Active Directory e, opcionalmente, para o Office 365 e automaticamente[outras aplicações SaaS suportadas pelo Azure AD](../manage-apps/user-provisioning.md).

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Quem é que esta solução de aprovisionamento de utilizador melhor adequado para?

Esta solução de aprovisionamento de utilizadores de Workday estão atualmente em pré-visualização pública e é ideal para:

* Organizações que desejam acesso uma solução previamente criada com base na cloud para aprovisionamento de utilizadores do Workday

* Organizações que precisam de aprovisionamento de utilizadores direta do Workday para o Active Directory ou do Azure Active Directory

* As organizações que exigem que os usuários a ser aprovisionado com dados obtidos a partir do módulo de Workday HCM (consulte [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html))

* As organizações que necessitem da associação, mover, e deixar que os utilizadores serão sincronizados para um ou mais florestas de diretório do Active Directory, domínios e UOs com base apenas no alterar informações detetadas no módulo do Workday HCM (consulte [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html))

* Organizações que usam o Office 365 para e-mail

## <a name="solution-architecture"></a>Arquitetura da Solução

Esta secção descreve a arquitetura da solução para ambientes de híbridos comuns de aprovisionamento de utilizadores de ponto-a-ponto. Existem dois fluxos relacionados:

* **Fluxo de dados de RH autoritativa – partir do Workday para o Active Directory no local:** Neste fluxo de trabalho (por exemplo, novas contratações, transferências, términos) primeiro ocorrem eventos na cloud de inquilino do Workday RH e, em seguida, os dados fluem para o Active Directory no local através do Azure AD e o agente de aprovisionamento. Consoante o evento, ele pode levar a operações criar/atualizar/Ativar/desativar no AD.
* **Fluxo de repetição de escrita do e-mail – do Active Directory no local para o dia de trabalho:** Assim que a criação de conta estiver concluída no Active Directory, ele está sincronizado com o Azure AD através do Azure AD Connect e o atributo de correio eletrónico obtido a partir do Active Directory pode ser escrito novamente no Workday.

![Descrição geral](./media/workday-inbound-tutorial/wd_overview.png)

### <a name="end-to-end-user-data-flow"></a>Fluxo de dados de utilizador de ponto a ponto

1. A equipe de RH realiza transações de trabalho (Joiners/manda/Leavers ou novas contratações/transferências/términos) no Workday HCM
2. O serviço de aprovisionamento do AD Azure executa sincronizações agendadas de identidades de RH do Workday e identifica as alterações que precisam de ser processados para sincronização com o Active Directory no local.
3. O serviço de aprovisionamento do AD Azure invoca o agente no local AAD Connect aprovisionamento com um payload de pedido que contém a operações de criar/atualizar/Ativar/desativar de conta do AD.
4. O agente do Azure AD Connect aprovisionamento utiliza uma conta de serviço para adicionar ou atualizar dados de conta do AD.
5. O Azure AD Connect / o motor de sincronização do AD executa a sincronização delta para receber atualizações no AD.
6. As atualizações do Active Directory são sincronizadas com o Azure Active Directory.
7. Se o conector de repetição de escrita do Workday está configurado, ele escrita-faz uma cópia de atributo de correio eletrónico no Workday, com base no atributo correspondente utilizado.

## <a name="planning-your-deployment"></a>Planear a implementação

Antes de iniciar a integração de Workday, verifique os pré-requisitos abaixo e leia as seguintes orientações sobre como de acordo com sua arquitetura do Active Directory e requisitos de aprovisionamento com a melhor ou soluções fornecida pelo Azure Active Directory do utilizador atual. Uma abrangente [plano de implantação](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-plans) com folhas de cálculo de planeamento também está disponível para ajudá-lo a colaborar com os seus parceiros de integração do Workday e partes interessadas de RH.

Esta secção abrange os seguintes aspetos de planeamento:

* [Pré-requisitos](#prerequisites)
* [Selecionar o aprovisionamento de conector de aplicações a implementar](#selecting-provisioning-connector-apps-to-deploy)
* [Planejamento de implantação do agente do Azure AD Connect de aprovisionamento](#planning-deployment-of-azure-ad-connect-provisioning-agent)
* [Integração com vários domínios do Active Directory](#integrating-with-multiple-active-directory-domains)
* [Planejamento do Workday para o mapeamento de atributo de utilizador do Active Directory e transformações](#planning-workday-to-active-directory-user-attribute-mapping-and-transformations)

### <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial parte do princípio de que já tem os seguintes itens:

* Uma subscrição superior com acesso de administrador global ou de válido do Azure AD Premium P1
* Um inquilino de implementação do Workday para fins de teste e integração
* Permissões de administrador no Workday para criar um utilizador de integração do sistema e fazer alterações para testar os dados de funcionários para fins de teste
* Para o aprovisionamento de utilizador para o Active Directory, um servidor com o Windows Server 2012 ou superior com o tempo de execução do .NET 4.7.1+ é necessário para alojar o [agente de aprovisionamento do local](https://go.microsoft.com/fwlink/?linkid=847801)
* [O Azure AD Connect](../hybrid/whatis-hybrid-identity.md) por sincronizar utilizadores entre o Active Directory e o Azure AD

### <a name="selecting-provisioning-connector-apps-to-deploy"></a>Selecionar o aprovisionamento de conector de aplicações a implementar

Para facilitar o provisionamento de fluxos de trabalho entre o dia de trabalho e o Active Directory, o Azure AD fornece várias aplicações de conector aprovisionamento que pode adicionar a partir da Galeria de aplicações do Azure AD:

![Galeria de aplicações do AAD](./media/workday-inbound-tutorial/wd_gallery.png)

* **Workday para o aprovisionamento de utilizadores do Active Directory** -conta aprovisionamento de utilizadores do Workday para um único domínio do Active Directory facilita a esta aplicação. Se tiver vários domínios, pode adicionar uma instância desta aplicação a partir da Galeria de aplicações do Azure AD para cada domínio do Active Directory que precisa de aprovisionar a.

* **Workday para o aprovisionamento de utilizador do Azure AD** – apesar do AAD Connect é a ferramenta que deve ser usada para sincronizar o Active Directory, os utilizadores ao Azure Active Directory, esta aplicação podem ser usados para facilitar o aprovisionamento de utilizadores apenas na cloud a partir do Workday para um único Azure Inquilino do Active Directory.

* **Repetição de escrita do workday** -repetição de escrita de endereços de e-mail do utilizador do Azure Active Directory para Workday facilita a esta aplicação.

> [!TIP]
> A aplicação regular de "Workday" é utilizada para configurar o início de sessão único entre o dia de trabalho e o Azure Active Directory.

Utilize o gráfico de fluxo de decisão abaixo para identificar que aplicações de aprovisionamento do Workday são relevantes para o seu cenário.
    ![Fluxograma de decisão](./media/workday-inbound-tutorial/wday_app_flowchart.png "fluxograma de decisão")

Utilize a tabela de conteúdos para ir para a secção relevante deste tutorial.

### <a name="planning-deployment-of-azure-ad-connect-provisioning-agent"></a>Planejamento de implantação do agente do Azure AD Connect de aprovisionamento

> [!NOTE]
> Esta secção é relevante apenas se planeja implantar o Workday para aplicação de aprovisionamento de utilizador de diretório Active Directory. Pode ignorar isto, se estiver a implementar a repetição de escrita do Workday ou Workday aplicação de aprovisionamento de utilizadores do Azure AD.

Dia de trabalho para o aprovisionamento de utilizadores do AD solução requer a implementação de um ou mais agentes de aprovisionamento em servidores que executam o Windows 2012 R2 ou superior com o mínimo de 4 GB de RAM e .NET 4.7.1+ tempo de execução. As seguintes considerações devem ser levadas em conta antes de instalar o agente de aprovisionamento:

* Certifique-se de que o servidor de anfitrião que executa o agente de aprovisionamento tem acesso à rede para o domínio de destino AD
* O Assistente de configuração do agente de aprovisionamento regista o agente no seu inquilino do Azure AD e o processo de registo requer acesso de *. msappproxy.net através da porta SSL 443. Certifique-se de que as regras de firewall de saída estão em vigor que permitem esta comunicação. O agente suporta [saída configuração de proxy HTTPS](#how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication).
* O agente de aprovisionamento utiliza uma conta de serviço para comunicar com no local domínios do AD. Antes da instalação do agente, recomenda-se que crie uma conta de serviço com permissões de administrador de domínio e uma palavra-passe não expira.  
* Durante a configuração do agente de aprovisionamento, pode selecionar os controladores de domínio que devem processar solicitações de provisionamento. Se tiver vários controladores de domínio distribuídos geograficamente, instale o agente de aprovisionamento no mesmo site que o controlador de domínio preferencial (es), para melhorar a fiabilidade e desempenho da solução ponto a ponto
* Para elevada disponibilidade, pode implementar mais do que um agente de aprovisionamento e registá-lo para lidar com o mesmo conjunto de locais domínios do AD.

> [!IMPORTANT]
> Em ambientes de produção, a Microsoft recomenda que tem um mínimo de 3 agentes aprovisionamento configurado com o inquilino do Azure AD para elevada disponibilidade.

### <a name="integrating-with-multiple-active-directory-domains"></a>Integração com vários domínios do Active Directory

> [!NOTE]
> Esta secção é relevante apenas se planeja implantar o Workday para aplicação de aprovisionamento de utilizador de diretório Active Directory. Pode ignorar isto, se estiver a implementar a repetição de escrita do Workday ou Workday aplicação de aprovisionamento de utilizadores do Azure AD.

Dependendo da topologia do Active Directory, terá de decidir o número de aplicações de conector de aprovisionamento de utilizador e o número de agentes de aprovisionamento para configurar. Abaixo encontram-se alguns dos padrões comuns de implantação que pode ser conhecido como planear a implementação.

#### <a name="deployment-scenario-1--single-workday-tenant---single-ad-domain"></a>Cenário de implementação #1: Único inquilino do Workday -> domínio única do AD

Neste cenário, tiver um inquilino do Workday e gostaria de aprovisionar utilizadores para um único domínio do AD de destino. Segue-se a configuração de produção recomendada para esta implementação.

|   |   |
| - | - |
| Não. de aprovisionamento de agentes para implementar no local | 3 (para elevada disponibilidade e ativação pós-falha) |
| Não. do Workday para as aplicações de aprovisionamento do AD utilizador configurar no portal do Azure | 1 |

  ![Cenário 1](./media/workday-inbound-tutorial/dep_scenario1.png)

#### <a name="deployment-scenario-2--single-workday-tenant---multiple-child-ad-domains"></a>Cenário de implementação #2: Único inquilino do Workday -> vários domínios de AD de subordinados

Este cenário envolve o aprovisionamento de utilizadores do Workday para vários domínios de subordinados de destino AD numa floresta. Segue-se a configuração de produção recomendada para esta implementação.

|   |   |
| - | - |
| Não. de aprovisionamento de agentes para implementar no local | 3 (para elevada disponibilidade e ativação pós-falha) |
| Não. do Workday para as aplicações de aprovisionamento do AD utilizador configurar no portal do Azure | uma aplicação por domínio subordinado |

  ![Cenário 2](./media/workday-inbound-tutorial/dep_scenario2.png)

#### <a name="deployment-scenario-3--single-workday-tenant---disjoint-ad-forests"></a>Cenário de implementação #3: Único inquilino do Workday -> florestas do AD não contíguo

Este cenário envolve o aprovisionamento de utilizadores do Workday para domínios em florestas do AD não contíguos. Segue-se a configuração de produção recomendada para esta implementação.

|   |   |
| - | - |
| Não. de aprovisionamento de agentes para implementar no local | 3 por floresta do AD não contíguo |
| Não. do Workday para as aplicações de aprovisionamento do AD utilizador configurar no portal do Azure | uma aplicação por domínio subordinado |

  ![Cenário 3](./media/workday-inbound-tutorial/dep_scenario3.png)

### <a name="planning-workday-to-active-directory-user-attribute-mapping-and-transformations"></a>Planejamento do Workday para o mapeamento de atributo de utilizador do Active Directory e transformações

> [!NOTE]
> Esta secção é relevante apenas se planeja implantar o Workday para aplicação de aprovisionamento de utilizador de diretório Active Directory. Pode ignorar isto, se estiver a implementar a repetição de escrita do Workday ou Workday aplicação de aprovisionamento de utilizadores do Azure AD.

Antes de configurar o aprovisionamento de utilizadores a um domínio do Active Directory, considere as seguintes perguntas. As respostas a essas perguntas determinará como os filtros de âmbito e mapeamentos de atributo tem de ser definido.

* **O que os utilizadores no Workday tem de ser aprovisionada para esta floresta do Active Directory?**

  * *Exemplo: Utilizadores onde o atributo do dia de trabalho "da empresa" contém o valor de "Contoso", e o atributo "Worker_Type" contém "Normais"*

* **Como são encaminhados os usuários em unidades diferentes organizacionais (UO)?**

  * *Exemplo: Os utilizadores são encaminhados para UOs que correspondem a uma localização do office, conforme definido no Workday "Município" e "Country_Region_Reference" atributos*

* **Como devem ser preenchidos os seguintes atributos no Active Directory?**

  * Nome comum (cn)
    * *Exemplo: Utilize o valor Workday User_ID, conforme definido pelos recursos humanos*

  * Identificação do funcionário (employeeId)
    * *Exemplo: Utilize o valor do Workday Worker_ID*

  * Nome da conta SAM (sAMAccountName)
    * *Exemplo: Utilize o valor Workday User_ID, filtrado por meio de um Azure AD aprovisionamento expressão para remover os carateres ilegais*

  * Nome do Principal de utilizador (userPrincipalName)
    * *Exemplo: Utilizar o valor Workday User_ID, com um Azure AD aprovisionamento expressão para acrescentar um nome de domínio*

* **Como devem ser correspondidos utilizadores entre o dia de trabalho e o Active Directory?**

  * *Exemplo: Utilizadores com um dia de trabalho específico "Worker_ID", valor são correspondidas com utilizadores do Active Directory em que "employeeID" tem o mesmo valor. Se o valor de Worker_ID não for encontrado no Active Directory, em seguida, crie um novo utilizador.*
  
* **Floresta do Active Directory já contém o usuário IDs necessários para a lógica correspondente para trabalhar?**

  * *Exemplo: Se esta configuração é uma nova implementação do Workday, recomenda-se que o Active Directory ser previamente preenchido com os valores corretos do Workday Worker_ID (ou valor de ID exclusivo da preferência) para manter a lógica correspondente mais simples possível.*

Como definir e configurar estas aplicações de conector de aprovisionamento especiais é o assunto das seções restantes deste tutorial. Que pode optar por configurar as aplicações dependem são de inquilinos que sistemas terá de aprovisionar e número de domínios do Active Directory e do Azure AD no seu ambiente.

## <a name="configure-integration-system-user-in-workday"></a>Configurar o utilizador de sistema de integração no Workday

Um requisito comum de todos os Workday conectores de aprovisionamento é que necessitem de credenciais de um utilizador de sistema de integração do Workday para ligar à API de recursos humanos do Workday. Esta secção descreve como criar um utilizador de sistema de integração no Workday e tem as seguintes secções:

* [Criar um utilizador de sistema de integração](#creating-an-integration-system-user)
* [Criar um grupo de segurança de integração](#creating-an-integration-security-group)
* [Configurar permissões de política de segurança de domínio](#configuring-domain-security-policy-permissions)
* [Configurar permissões de política de segurança de processo de negócio](#configuring-business-process-security-policy-permissions)
* [Ativar as alterações de política de segurança](#activating-security-policy-changes)

> [!NOTE]
> É possível ignorar este procedimento em vez disso, uma conta de administrador global do Workday que a conta de integração do sistema. Isso pode funcionar bem para demonstrações, mas não é recomendado para implementações de produção.

### <a name="creating-an-integration-system-user"></a>Criar um utilizador de sistema de integração

**Para criar um utilizador de sistema de integração:**

1. Inicie sessão no seu inquilino do Workday através de uma conta de administrador. Na **Workday aplicativo**, introduza criar utilizador na caixa de pesquisa e, em seguida, clique em **criar utilizador do sistema de integração**.

    ![Criar utilizador](./media/workday-inbound-tutorial/wd_isu_01.png "criar utilizador")
2. Concluir o **criar utilizador do sistema de integração** tarefa ao fornecer um nome de utilizador e palavra-passe para um novo utilizador do sistema de integração.  
  
* Deixe o **requerem nova palavra-passe no próximo sessão** opção desmarcada, uma vez que este utilizador será logon por meio de programação.
* Deixe o **minutos de tempo limite da sessão** com o respetivo valor predefinido de 0, que irá impedir sessões do usuário exceder o tempo limite prematuramente.
* Selecione a opção **fazer permitir sessões de interface do Usuário** pois ele fornece uma camada adicional de segurança que impede que um utilizador com a palavra-passe do sistema de integração de iniciar sessão no Workday.

    ![Criar utilizador do sistema de integração](./media/workday-inbound-tutorial/wd_isu_02.png "criar utilizador do sistema de integração")

### <a name="creating-an-integration-security-group"></a>Criar um grupo de segurança de integração

Neste passo, irá criar um grupo de segurança do sistema de integração restrita ou não no Workday e atribua o utilizador de sistema de integração criado no passo anterior a este grupo.

**Para criar um grupo de segurança:**

1. Introduza criar grupo de segurança na caixa de pesquisa e, em seguida, clique em **criar grupo de segurança**.

    ![Grupo de CreateSecurity](./media/workday-inbound-tutorial/wd_isu_03.png "CreateSecurity grupo")
2. Concluir o **criar grupo de segurança** tarefas. 

   * Existem dois tipos de grupos de segurança no Workday:
     * **Restrita:** Todos os membros do grupo de segurança podem aceder a todas as instâncias de dados protegidas pelo grupo de segurança.
     * **Restrita:** Todos os membros do grupo de segurança têm acesso contextual a um subconjunto de instâncias de dados (linhas) que o grupo de segurança pode aceder.
   * Entre em contacto com seu parceiro de integração do Workday para selecionar o tipo de grupo de segurança adequados para a integração.
   * Sabe o tipo de grupo, selecione **grupo de segurança de sistema de integração (Unconstrained)** ou **grupo de segurança de sistema de integração (Constrained)** partir o **tipo de inquilinos grupo de segurança**  lista pendente.

     ![Grupo de CreateSecurity](./media/workday-inbound-tutorial/wd_isu_04.png "CreateSecurity grupo")

3. Após a criação do grupo de segurança com êxito, verá uma página onde a pode atribuir membros ao grupo de segurança. Adicione o novo utilizador de sistema de integração criado no passo anterior a este grupo de segurança. Se estiver a utilizar *restrita* grupo de segurança, também será necessário selecionar o âmbito da organização adequada.

    ![Editar grupo de segurança](./media/workday-inbound-tutorial/wd_isu_05.png "Editar grupo de segurança")

### <a name="configuring-domain-security-policy-permissions"></a>Configurar permissões de política de segurança de domínio

Neste passo, terá de conceder permissões de política para os dados de trabalho para o grupo de segurança "segurança de domínio".

**Para configurar permissões de política de segurança de domínio:**

1. Introduza **configuração de segurança do domínio** na caixa de pesquisa e, em seguida, clique no link **relatório de configuração de segurança do domínio**.  

    ![As políticas de segurança do domínio](./media/workday-inbound-tutorial/wd_isu_06.png "políticas de segurança de domínio")  
2. Na **domínio** caixa de texto, procure os seguintes domínios e adicioná-los para o filtro individualmente.  
   * *Aprovisionamento de conta externa*
   * *Dados de trabalho: Relatórios de trabalho público*
   * *Dados de pessoa: Informações de contacto de trabalho*
   * *Dados de trabalho: Todas as posições*
   * *Dados de trabalho: Informações de equipe atuais*
   * *Dados de trabalho: Título de negócios no perfil de trabalho*

     ![As políticas de segurança do domínio](./media/workday-inbound-tutorial/wd_isu_07.png "políticas de segurança de domínio")  

     ![As políticas de segurança do domínio](./media/workday-inbound-tutorial/wd_isu_08.png "políticas de segurança de domínio") 

     Clique em **OK**.

3. No relatório que aparece, selecione as reticências (...), que é apresentado junto a **aprovisionamento da conta externa** e clique na opção de menu **domínio -> Editar permissões de política de segurança**

    ![As políticas de segurança do domínio](./media/workday-inbound-tutorial/wd_isu_09.png "políticas de segurança de domínio")  

4. Sobre o **editar permissões de política de segurança de domínio** página, desloque para baixo para a seção **permissões de integração**. Clique no sinal "+" para adicionar o grupo de sistema de integração para a lista de grupos de segurança com **Obtenha** e **colocar** permissões de integração.

    ![Editar permissão](./media/workday-inbound-tutorial/wd_isu_10.png "Editar permissão")  

5. Clique no sinal "+" para adicionar o grupo de sistema de integração para a lista de grupos de segurança com **Obtenha** e **colocar** permissões de integração.

    ![Editar permissão](./media/workday-inbound-tutorial/wd_isu_11.png "Editar permissão")  

6. Repita os passos 3 a 5 acima para cada uma destas políticas de segurança restantes:

   | Operação | Política de segurança de domínio |
   | ---------- | ---------- |
   | Get e Put | Dados de trabalho: Relatórios de trabalho público |
   | Get e Put | Dados de pessoa: Informações de contacto de trabalho |
   | Get | Dados de trabalho: Todas as posições |
   | Get | Dados de trabalho: Informações de equipe atuais |
   | Get | Dados de trabalho: Título de negócios no perfil de trabalho |

### <a name="configuring-business-process-security-policy-permissions"></a>Configurar permissões de política de segurança de processo de negócio

Neste passo, terá de conceder permissões de política para os dados de trabalho para o grupo de segurança "segurança do processo de negócio". Este passo é necessário para configurar o conector de aplicações de repetição de escrita do Workday.

**Para configurar permissões de política de segurança de processos de negócio:**

1. Introduza **política de processo de negócio** na caixa de pesquisa e, em seguida, clique no link **Editar política de segurança de processo de negócio** tarefas.  

    ![As políticas de segurança do processo de negócio](./media/workday-inbound-tutorial/wd_isu_12.png "políticas de segurança do processo de negócio")  

2. Na **tipo de processo de negócio** caixa de texto, procure *contacto* e selecione **contacto alteração** processo empresarial e clique em **OK**.

    ![As políticas de segurança do processo de negócio](./media/workday-inbound-tutorial/wd_isu_13.png "políticas de segurança do processo de negócio")  

3. Sobre o **Editar política de segurança de processo de negócio** página, desloque-se para o **manter informações de contacto (serviço Web)** secção.

    ![As políticas de segurança do processo de negócio](./media/workday-inbound-tutorial/wd_isu_14.png "políticas de segurança do processo de negócio")  

4. Selecione e adicione o novo grupo de segurança do sistema de integração para a lista de grupos de segurança que podem iniciar a solicitação de serviços da web. Clique em **feito**. 

    ![As políticas de segurança do processo de negócio](./media/workday-inbound-tutorial/wd_isu_15.png "políticas de segurança do processo de negócio")  

### <a name="activating-security-policy-changes"></a>Ativar as alterações de política de segurança

**Para ativar as alterações de política de segurança:**

1. Introduza ativar na caixa de pesquisa e, em seguida, clique no link **ativar alterações de política de segurança pendentes**.

    ![Ativar](./media/workday-inbound-tutorial/wd_isu_16.png "ativar")

1. Comece a tarefa ativar alterações de política de segurança pendentes por introduzir um comentário para fins de auditoria e, em seguida, clique em **OK**.
1. Concluir a tarefa no ecrã seguinte, marcando a caixa de verificação **confirmar**e, em seguida, clique em **OK**.

    ![Ativar pendentes de segurança](./media/workday-inbound-tutorial/wd_isu_18.png "ativar pendentes de segurança")  

## <a name="configuring-user-provisioning-from-workday-to-active-directory"></a>Configurar o aprovisionamento de utilizadores do Workday para o Active Directory

Esta secção fornece os passos para a conta aprovisionamento de utilizadores do Workday para cada domínio do Active Directory dentro do escopo de sua integração.

* [Instalar e configurar os agentes de aprovisionamento no local](#part-1-install-and-configure-on-premises-provisioning-agents)
* [Adicionar a aplicação de conector de aprovisionamento e criar a ligação ao Workday](#part-2-adding-the-provisioning-connector-app-and-creating-the-connection-to-workday)
* [Configurar mapeamentos de atributos](#part-3-configure-attribute-mappings)
* [Ativar e iniciar o aprovisionamento de utilizadores](#enable-and-launch-user-provisioning)

### <a name="part-1-install-and-configure-on-premises-provisioning-agents"></a>Parte 1: Instalar e configurar os agentes de aprovisionamento no local

Para aprovisionar ao Active Directory no local, um agente deve ser instalado num servidor que tenha 4.7.1+ de .NET Framework e de rede de acesso para os domínios do Active Directory pretendidos.

> [!TIP]
> Pode verificar a versão do .NET framework no seu servidor com as instruções fornecidas [aqui](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed).
> Se o servidor não tem o .NET 4.7.1 ou superior instalado, pode transferi-lo no [aqui](https://support.microsoft.com/help/4033342/the-net-framework-4-7-1-offline-installer-for-windows).  

Assim que tiver implementado 4.7.1+ de .NET, pode baixar o **[agente aprovisionamento aqui locais](https://go.microsoft.com/fwlink/?linkid=847801)** e siga os passos abaixo para concluir a configuração do agente.

1. Inicie sessão no Windows Server onde pretende instalar o agente de novo.
2. Inicie o instalador do agente de aprovisionamento, aceitar os termos e clique nas **instalar** botão.

   ![Instalar o ecrã](./media/workday-inbound-tutorial/pa_install_screen_1.png "instalar ecrã")
3. Após a instalação estiver concluída, será iniciado o assistente e verá a **Connect do Azure AD** ecrã. Clique nas **Authenticate** botão para ligar à sua instância do Azure AD.

   ![Connect Azure AD](./media/workday-inbound-tutorial/pa_install_screen_2.png "Connect Azure AD")
1. Autenticar-se à sua instância do Azure AD com credenciais de Administrador Global.

   ![Autenticação de administrador](./media/workday-inbound-tutorial/pa_install_screen_3.png "autenticação de Admin")

> [!NOTE]
> As credenciais de administrador do Azure AD é utilizada apenas para ligar ao seu inquilino do Azure AD. O agente não armazena as credenciais localmente no servidor.

1. Após a autenticação com êxito com o Azure AD, verá os **ligar o Active Directory** ecrã. Neste passo, introduza o seu nome de domínio do AD e clique nas **Adicionar diretório** botão.

   ![Adicionar diretório](./media/workday-inbound-tutorial/pa_install_screen_4.png "Adicionar diretório")
  
1. Agora será solicitado para introduzir as credenciais necessárias para ligar ao domínio do AD. No mesmo ecrã, pode utilizar o **selecione a prioridade de controlador de domínio** para especificar controladores de domínio que o agente irá usar para o envio de solicitações de provisionamento.

   ![Credenciais de domínio](./media/workday-inbound-tutorial/pa_install_screen_5.png)
1. Depois de configurar o domínio, o instalador apresenta uma lista de domínios configurados. Neste ecrã, pode repetir o passo #5 e 6 # para adicionar mais domínios ou clique em **seguinte** para avançar para o registo do agente.

   ![Configurar domínios](./media/workday-inbound-tutorial/pa_install_screen_6.png "configurado domínios")

   > [!NOTE]
   > Se tiver vários domínios de AD (por exemplo, na.contoso.com, emea.contoso.com), adicione cada domínio individualmente à lista. Não é suficiente apenas a adição de domínio principal (por exemplo, contoso.com). Deve registrar cada domínio subordinado com o agente.
1. Reveja os detalhes de configuração e clique em **confirmar** para registar o agente.
  
   ![Confirmar a tela](./media/workday-inbound-tutorial/pa_install_screen_7.png "confirmar ecrã")
1. O Assistente de configuração apresenta o progresso do registo do agente.
  
   ![Registo do agente](./media/workday-inbound-tutorial/pa_install_screen_8.png "registo do agente")
1. Assim que o registo do agente é efetuada com êxito, pode clicar em **sair** para sair do assistente.
  
   ![Sair do ecrã](./media/workday-inbound-tutorial/pa_install_screen_9.png "sair do ecrã")
1. Verificar a instalação do agente e certifique-se de que está em execução ao abrir o Snap-In "Serviços" e procure o serviço com o nome "Microsoft Azure AD Connect aprovisionamento Agent"
  
   ![Serviços](./media/workday-inbound-tutorial/services.png)

### <a name="part-2-adding-the-provisioning-connector-app-and-creating-the-connection-to-workday"></a>Parte 2: Adicionar a aplicação de conector de aprovisionamento e criar a ligação ao Workday

**Para configurar o Workday para o aprovisionamento do Active Directory:**

1. Ir para <https://portal.azure.com>

2. Na barra de navegação esquerdo, selecione **do Azure Active Directory**

3. Selecione **aplicações empresariais**, em seguida, **todos os aplicativos**.

4. Selecione **adicionar uma aplicação**e selecione o **todos os** categoria.

5. Procure **Workday Provisioning para Active Directory**e adicionar essa aplicação a partir da galeria.

6. Depois da aplicação é adicionada e o ecrã de detalhes da aplicação é apresentado, selecione **aprovisionamento**

7. Alteração da **aprovisionamento** **modo** para **automática**

8. Concluir o **credenciais de administrador** secção da seguinte forma:

   * **Nome de utilizador administrador** – introduza o nome de utilizador da conta de sistema de integração do Workday, com o nome de domínio de inquilino anexado. Ele deve ser algo semelhante: **nome de utilizador\@nome_do_inquilino**

   * **Palavra-passe de administrador –** introduza a palavra-passe da conta de sistema de integração do Workday

   * **URL – de inquilinos** introduza o URL para o ponto de extremidade do serviços de web do Workday para o seu inquilino. Este valor deve ser semelhante a: https://wd3-impl-services1.workday.com/ccx/service/contoso4, onde *contoso4* é substituída pelo nome do seu inquilino correto e *wd3 impl* é substituído com a cadeia de caracteres do ambiente correto.

   * **Floresta de diretório do Active Directory -** o "nome" do seu domínio do Active Directory, como registrado com o agente. Utilize a lista pendente para selecionar o domínio de destino para o aprovisionamento. Este valor é, normalmente, uma cadeia de caracteres, como: *contoso.com*

   * **Contentor do Active Directory -** introduzir o DN do contêiner em que o agente deve criar contas de utilizador por predefinição.
        Exemplo: *UO = usuários padrão, UO = Users, DC = contoso, DC = test*
     > [!NOTE]
     > Esta definição só entra em jogo para criação de contas de utilizador se o *parentDistinguishedName* atributo não é configurado nos mapeamentos de atributos. Esta definição não é utilizada para pesquisa de usuário ou atualizar operações. A árvore de sub-rotina de todo o domínio está no âmbito da operação de pesquisa.

   * **E-Mail de notificação –** introduza o seu endereço de e-mail e marque a caixa de verificação "enviar e-mail se ocorrer uma falha".

> [!NOTE]
> O serviço de aprovisionamento do AD do Azure envia a notificação por e-mail se o trabalho de aprovisionamento vai para um [quarentena](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning#quarantine) estado.

   * Clique nas **Testar ligação** botão. Se o teste de ligação for bem-sucedida, clique nas **guardar** botão na parte superior. Se falhar, verifique novamente que as credenciais do Workday e as credenciais de AD configuradas na configuração de agente são válidas.

     ![Portal do Azure](./media/workday-inbound-tutorial/wd_1.png)

   * Depois das credenciais são guardadas com êxito, o **mapeamentos** secção irá apresentar o mapeamento predefinido **sincronizar trabalhadores do Workday no local do Active Directory**

### <a name="part-3-configure-attribute-mappings"></a>Parte 3: Configurar mapeamentos de atributos

Nesta secção, irá configurar como os dados de utilizador fluem a partir do Workday para o Active Directory.

1. No separador aprovisionamento em **mapeamentos**, clique em **sincronizar trabalhadores do Workday no local do Active Directory**.

2. Na **âmbito de objeto de origem** campo, pode selecionar quais conjuntos de utilizadores no Workday devem estar no âmbito de aprovisionamento para o AD, definindo um conjunto de filtros baseados em atributo. O escopo de padrão é "todos os utilizadores no Workday". Filtros de exemplo:

   * Exemplo: Âmbito para utilizadores com IDs de trabalho entre 1000000 e 2000000

      * Atributo: WorkerID

      * Operador: Correspondência REGEX

      * Valor: (1[0-9][0-9][0-9][0-9][0-9][0-9])

   * Exemplo: Apenas os funcionários e não os operadores de contingência

      * Atributo: employeeID

      * Operador: IS NOT NULL

> [!TIP]
> Quando estiver a configurar a aplicação de aprovisionamento pela primeira vez, terá de testar e verificar seus mapeamentos de atributos e as expressões para se certificar de que ele está lhe dando o resultado desejado. A Microsoft recomenda utilizar o controlo de âmbito filtra sob **âmbito de objeto de origem** para testar seus mapeamentos com alguns utilizadores de teste do Workday. Uma vez que tiver verificado que os mapeamentos de trabalho, em seguida, pode remover o filtro ou gradualmente expandi-la para incluir mais usuários.

1. Na **ações do objeto de destino** campo, globalmente pode filtrar quais ações são executadas no Active Directory. **Crie** e **atualização** são mais comuns.

1. Na **mapeamentos de atributo** secção, pode definir o dia de trabalho individual como atributos do mapa para atributos do Active Directory.

1. Clique num mapeamento de atributo existente para atualizá-lo ou clique em **adicionar novo mapeamento** na parte inferior do ecrã, para adicionar novos mapeamentos. Um mapeamento do atributo individual suporta estas propriedades:

      * **Tipo de mapeamento**

         * **Direto** – escreve o valor do atributo do Workday para o atributo de AD, sem alterações

         * **Constante** -escrever um valor de cadeia de caracteres constante, estático para o atributo de AD

         * **Expressão** – permite-lhe escrever um valor personalizado para o atributo de AD, com base num ou mais atributos do Workday. [Para obter mais informações, veja este artigo sobre expressões](../manage-apps/functions-for-customizing-application-data.md).

      * **Atributo de origem** -o atributo de utilizador a partir do Workday. Se o atributo que procura não estiver presente, veja [personalizar a lista de atributos de utilizador do Workday](#customizing-the-list-of-workday-user-attributes).

      * **Valor predefinido** – opcional. Se o atributo de origem tem um valor vazio, o mapeamento de escrever este valor em vez disso.
            Configuração mais comuns é deixar em branco.

      * **Atributo de destino** – o atributo de utilizador no Active Directory.

      * **Correspondência de objetos utilizando este atributo** – independentemente desse mapeamento deve ser utilizado para identificar exclusivamente os utilizadores entre o dia de trabalho e o Active Directory. Normalmente, este valor é definido no campo ID de função de trabalho para Workday, que normalmente é mapeado para um dos atributos de identificação do funcionário no Active Directory.

      * **Precedência de correspondência** – o várias correspondência de atributos pode ser definida. Quando houver múltiplos, eles são avaliados na ordem definida por este campo. Assim que uma correspondência for encontrada, não são necessárias mais correspondência de atributos são avaliados.

      * **Aplicar este mapeamento**

         * **Sempre** – aplicar este mapeamento em ambos os criação de utilizador e ações de atualização

         * **Apenas durante a criação** -aplicar este mapeamento apenas nas ações de criação do utilizador

1. Para guardar os seus mapeamentos, clique em **guardar** na parte superior da seção de mapeamento do atributo.

   ![Portal do Azure](./media/workday-inbound-tutorial/wd_2.png)

#### <a name="below-are-some-example-attribute-mappings-between-workday-and-active-directory-with-some-common-expressions"></a>Seguem-se alguns exemplos mapeamentos de atributos entre o dia de trabalho e o Active Directory, com algumas expressões comuns

* A expressão que mapeia para o *parentDistinguishedName* atributo é utilizado para aprovisionamento de um utilizador para diferentes UOs com base num ou mais atributos de origem do Workday. Este exemplo coloca os usuários em diferentes UOs com base em que Localidade se encontram.

* O *userPrincipalName* atributo no Active Directory é gerado usando a função de eliminação de duplicação [SelectUniqueValue](../manage-apps/functions-for-customizing-application-data.md#selectuniquevalue) que verifica a existência de um valor gerado no domínio de destino AD e apenas Define-se que seja exclusivo.  

* [Há documentação sobre como escrever expressões aqui](../manage-apps/functions-for-customizing-application-data.md). Esta secção inclui exemplos sobre como remover os carateres especiais.

| ATRIBUTO DE WORKDAY | ATRIBUTO DO ACTIVE DIRECTORY |  CORRESPONDÊNCIA DE ID? | CRIAR / ATUALIZAR |
| ---------- | ---------- | ---------- | ---------- |
| **WorkerID**  |  employeeID | **Sim** | Escrito na criação de apenas |
| **PreferredNameData**    |  CN    |   |   Escrito na criação de apenas |
| **SelectUniqueValue (associação ("\@", Junte-se a (".", \[FirstName\], \[LastName\]), "contoso.com"), Junte-se a ("\@", Junte-se a (".", Mid (\[FirstName\], 1, 1 (), \[LastName\]), "contoso.com"), Junte-se a ("\@", Junte-se a (".", Mid (\[FirstName\], 1, 2), \[LastName\]), "contoso.com"))**   | userPrincipalName     |     | Escrito na criação de apenas 
| **Replace(Mid(Replace(\[UserID\], , "(\[\\\\/\\\\\\\\\\\\\[\\\\\]\\\\:\\\\;\\\\\|\\\\=\\\\,\\\\+\\\\\*\\\\?\\\\&lt;\\\\&gt;\])", , "", , ), 1, 20), , "([\\\\.)\*\$](file:///\\.)*$)", , "", , )**      |    sAMAccountName            |     |         Escrito na criação de apenas |
| **Switch(\[Active\], , "0", "True", "1", "False")** |  accountDisabled      |     | Criar + atualizar |
| **FirstName**   | givenName       |     |    Criar + atualizar |
| **LastName**   |   sn   |     |  Criar + atualizar |
| **PreferredNameData**  |  displayName |     |   Criar + atualizar |
| **Empresa**         | Empresa   |     |  Criar + atualizar |
| **SupervisoryOrganization**  | Departamento  |     |  Criar + atualizar |
| **ManagerReference**   | gestor  |     |  Criar + atualizar |
| **BusinessTitle**   |  título     |     |  Criar + atualizar | 
| **AddressLineData**    |  streetAddress  |     |   Criar + atualizar |
| **Município**   |   l   |     | Criar + atualizar |
| **CountryReferenceTwoLetter**      |   Co |     |   Criar + atualizar |
| **CountryReferenceTwoLetter**    |  c  |     |         Criar + atualizar |
| **CountryRegionReference** |  St     |     | Criar + atualizar |
| **WorkSpaceReference** | physicalDeliveryOfficeName    |     |  Criar + atualizar |
| **PostalCode**  |   postalCode  |     | Criar + atualizar |
| **PrimaryWorkTelephone**  |  telephoneNumber   |     | Criar + atualizar |
| **Fax**      | facsimileTelephoneNumber     |     |    Criar + atualizar |
| **móvel**  |    móvel       |     |       Criar + atualizar |
| **LocalReference** |  preferredLanguage  |     |  Criar + atualizar |                                               
| **Comutador (\[município\], "UO = usuários padrão, UO = Users, UO = Default, UO = localizações, DC = contoso, DC = com", "Dallas", "UO = para os usuários padrão, a UO = Users, UO = Dallas, UO = localizações, DC = contoso, DC = com", "Austin", "UO = para os usuários padrão, a UO = Users, UO = Austin, UO = localizações, DC = contoso, DC = com ","Seattle"," UO = para os usuários padrão, a UO = Users, UO = Seattle, UO = localizações, DC = contoso, DC = com ","Londres"," UO = usuários padrão, UO = Users, UO = Londres, UO = localizações, DC = contoso, DC = com ")**  | parentDistinguishedName     |     |  Criar + atualizar |

Assim que a configuração de mapeamento do atributo estiver concluída, pode agora [ativar e iniciar o serviço de aprovisionamento de utilizador](#enable-and-launch-user-provisioning).

## <a name="configuring-user-provisioning-to-azure-ad"></a>Configurar o aprovisionamento para o Azure AD

As secções seguintes descrevem os passos para configurar o aprovisionamento de utilizadores do Workday para o Azure AD para implementações apenas na cloud.

* [Adicionar a aplicação do conector de aprovisionamento do Azure AD e criar a ligação ao Workday](#part-1-adding-the-azure-ad-provisioning-connector-app-and-creating-the-connection-to-workday)
* [Configurar mapeamentos de atributos do Workday e o Azure AD](#part-2-configure-workday-and-azure-ad-attribute-mappings)
* [Ativar e iniciar o aprovisionamento de utilizadores](#enable-and-launch-user-provisioning)

> [!IMPORTANT]
> Apenas siga o procedimento a seguir se tiver utilizadores de apenas na cloud que necessitam de ser aprovisionada para o Azure AD e não no local do Active Directory.

### <a name="part-1-adding-the-azure-ad-provisioning-connector-app-and-creating-the-connection-to-workday"></a>Parte 1: Adicionar a aplicação do conector de aprovisionamento do Azure AD e criar a ligação ao Workday

**Para configurar o Workday para o aprovisionamento do Azure Active Directory para utilizadores apenas na cloud:**

1. Aceda a <https://portal.azure.com>.

2. Na barra de navegação esquerdo, selecione **do Azure Active Directory**

3. Selecione **aplicações empresariais**, em seguida, **todos os aplicativos**.

4. Selecione **adicionar uma aplicação**e, em seguida, selecione a **todos os** categoria.

5. Procure **Workday para o aprovisionamento do Azure AD**e adicionar essa aplicação a partir da galeria.

6. Depois da aplicação é adicionada e o ecrã de detalhes da aplicação é apresentado, selecione **aprovisionamento**

7. Alteração da **aprovisionamento** **modo** para **automática**

8. Concluir o **credenciais de administrador** secção da seguinte forma:

   * **Nome de utilizador administrador** – introduza o nome de utilizador da conta de sistema de integração do Workday, com o nome de domínio de inquilino anexado. Deve ser algo semelhante: username@contoso4

   * **Palavra-passe de administrador –** introduza a palavra-passe da conta de sistema de integração do Workday

   * **URL – de inquilinos** introduza o URL para o ponto de extremidade do serviços de web do Workday para o seu inquilino. Este valor deve ser semelhante a: https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources, onde *contoso4* é substituída pelo nome do seu inquilino correto e *wd3 impl* é substituído com a cadeia de caracteres do ambiente correto. Se este URL não é conhecido, trabalhe em conjunto com o seu representante de suporte ou de parceiro de integração de Workday para determinar o URL correto a utilizar.

   * **E-Mail de notificação –** introduza o seu endereço de e-mail e marque a caixa de verificação "enviar e-mail se ocorrer uma falha".

   * Clique nas **Testar ligação** botão.

   * Se o teste de ligação for bem-sucedida, clique nas **guardar** botão na parte superior. Se falhar, verifique novamente que o URL de dia de trabalho e as credenciais são válidas no Workday.

### <a name="part-2-configure-workday-and-azure-ad-attribute-mappings"></a>Parte 2: Configurar mapeamentos de atributos do Workday e o Azure AD

Nesta secção, irá configurar como os dados de utilizador fluem do Workday para o Azure Active Directory para utilizadores apenas na cloud.

1. No separador aprovisionamento em **mapeamentos**, clique em **sincronizar funções de trabalho para o Azure AD**.

2. Na **âmbito de objeto de origem** campo, pode selecionar quais conjuntos de utilizadores no Workday devem estar no âmbito de aprovisionamento para o Azure AD, definindo um conjunto de filtros baseados em atributo. O escopo de padrão é "todos os utilizadores no Workday". Filtros de exemplo:

   * Exemplo: Âmbito para utilizadores com IDs de trabalho entre 1000000 e 2000000

      * Atributo: WorkerID

      * Operador: Correspondência REGEX

      * Valor: (1[0-9][0-9][0-9][0-9][0-9][0-9])

   * Exemplo: Apenas os operadores de contingência e os funcionários não regulares

      * Atributo: ContingentID

      * Operador: IS NOT NULL

3. Na **ações do objeto de destino** campo, globalmente pode filtrar quais ações são efetuadas no Azure AD. **Crie** e **atualização** são mais comuns.

4. Na **mapeamentos de atributo** secção, pode definir o dia de trabalho individual como atributos do mapa para atributos do Active Directory.

5. Clique num mapeamento de atributo existente para atualizá-lo ou clique em **adicionar novo mapeamento** na parte inferior do ecrã, para adicionar novos mapeamentos. Um mapeamento do atributo individual suporta estas propriedades:

   * **Tipo de mapeamento**

      * **Direto** – escreve o valor do atributo do Workday para o atributo de AD, sem alterações

      * **Constante** -escrever um valor de cadeia de caracteres constante, estático para o atributo de AD

      * **Expressão** – permite-lhe escrever um valor personalizado para o atributo de AD, com base num ou mais atributos do Workday. [Para obter mais informações, veja este artigo sobre expressões](../manage-apps/functions-for-customizing-application-data.md).

   * **Atributo de origem** -o atributo de utilizador a partir do Workday. Se o atributo que procura não estiver presente, veja [personalizar a lista de atributos de utilizador do Workday](#customizing-the-list-of-workday-user-attributes).

   * **Valor predefinido** – opcional. Se o atributo de origem tem um valor vazio, o mapeamento de escrever este valor em vez disso.
            Configuração mais comuns é deixar em branco.

   * **Atributo de destino** – o atributo de utilizador no Azure AD.

   * **Correspondência de objetos utilizando este atributo** – independentemente desse atributo deve ser utilizado para identificar exclusivamente os utilizadores entre o dia de trabalho e o Azure AD. Normalmente, este valor é definido no campo ID de função de trabalho para Workday, que normalmente é mapeado para o atributo de identificação do funcionário (novo) ou um atributo de extensão no Azure AD.

   * **Precedência de correspondência** – o várias correspondência de atributos pode ser definida. Quando houver múltiplos, eles são avaliados na ordem definida por este campo. Assim que uma correspondência for encontrada, não são necessárias mais correspondência de atributos são avaliados.

   * **Aplicar este mapeamento**

     * **Sempre** – aplicar este mapeamento em ambos os criação de utilizador e ações de atualização

     * **Apenas durante a criação** -aplicar este mapeamento apenas nas ações de criação do utilizador

6. Para guardar os seus mapeamentos, clique em **guardar** na parte superior da seção de mapeamento do atributo.

Assim que a configuração de mapeamento do atributo estiver concluída, pode agora [ativar e iniciar o serviço de aprovisionamento de utilizador](#enable-and-launch-user-provisioning).

## <a name="configuring-writeback-of-email-addresses-to-workday"></a>Configurar a repetição de escrita de endereços de e-mail para Workday

Siga estas instruções para configurar a repetição de escrita de endereços de e-mail do utilizador do Azure Active Directory para Workday.

* [Adicionar a aplicação de conector de repetição de escrita e criar a ligação ao Workday](#part-1-adding-the-writeback-connector-app-and-creating-the-connection-to-workday)
* [Configurar mapeamentos de atributos de repetição de escrita](#part-2-configure-writeback-attribute-mappings)
* [Ativar e iniciar o aprovisionamento de utilizadores](#enable-and-launch-user-provisioning)

### <a name="part-1-adding-the-writeback-connector-app-and-creating-the-connection-to-workday"></a>Parte 1: Adicionar a aplicação de conector de repetição de escrita e criar a ligação ao Workday

**Para configurar o conector de repetição de escrita do dia de trabalho:**

1. Ir para <https://portal.azure.com>

2. Na barra de navegação esquerdo, selecione **do Azure Active Directory**

3. Selecione **aplicações empresariais**, em seguida, **todos os aplicativos**.

4. Selecione **adicionar uma aplicação**, em seguida, selecione a **todos os** categoria.

5. Procure **repetição de escrita do Workday**e adicionar essa aplicação a partir da galeria.

6. Depois da aplicação é adicionada e o ecrã de detalhes da aplicação é apresentado, selecione **aprovisionamento**

7. Alteração da **aprovisionamento** **modo** para **automática**

8. Concluir o **credenciais de administrador** secção da seguinte forma:

   * **Nome de utilizador administrador** – introduza o nome de utilizador da conta de sistema de integração do Workday, com o nome de domínio de inquilino anexado. Deve ser algo semelhante: *nome de utilizador\@contoso4*

   * **Palavra-passe de administrador –** introduza a palavra-passe da conta de sistema de integração do Workday

   * **URL – de inquilinos** introduza o URL para o ponto de extremidade do serviços de web do Workday para o seu inquilino. Este valor deve ser semelhante a: https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources, onde *contoso4* é substituída pelo nome do seu inquilino correto e *wd3 impl* é substituído com a cadeia de caracteres do ambiente correto (se necessário).

   * **E-Mail de notificação –** introduza o seu endereço de e-mail e marque a caixa de verificação "enviar e-mail se ocorrer uma falha".

   * Clique nas **Testar ligação** botão. Se o teste de ligação for bem-sucedida, clique nas **guardar** botão na parte superior. Se falhar, verifique novamente que o URL de dia de trabalho e as credenciais são válidas no Workday.

### <a name="part-2-configure-writeback-attribute-mappings"></a>Parte 2: Configurar mapeamentos de atributos de repetição de escrita

Nesta secção, irá configurar como fluxo de atributos de repetição de escrita do Azure AD para Workday.

1. No separador aprovisionamento em **mapeamentos**, clique em **sincronizar utilizadores do Azure Active Directory para Workday**.

2. Na **âmbito de objeto de origem** campo, pode, opcionalmente, filtrar, quais conjuntos de utilizadores no Azure Active Directory devem ter os respetivos endereços de e-mail a repetição de escrita no Workday. O escopo de padrão é "todos os utilizadores no Azure AD".

3. Na **mapeamentos de atributo** secção, atualize o ID correspondente para indicar o atributo no Azure Active Directory onde está armazenada o ID de trabalho do dia de trabalho ou a identificação do funcionário. É um método correspondente popular sincronizar o ID de trabalho de dia de trabalho ou a identificação do funcionário para extensionAttribute1 15 no Azure AD e, em seguida, utilizar esse atributo no Azure AD para corresponder a utilizadores no Workday.

4. Para guardar os seus mapeamentos, clique em **guardar** na parte superior da seção de mapeamento do atributo.

Assim que a configuração de mapeamento do atributo estiver concluída, pode agora [ativar e iniciar o serviço de aprovisionamento de utilizador](#enable-and-launch-user-provisioning). 

## <a name="enable-and-launch-user-provisioning"></a>Ativar e iniciar o aprovisionamento de utilizadores

Assim que as configurações de aplicação de aprovisionamento de dia de trabalho concluídas, pode ativar o serviço de aprovisionamento no portal do Azure.

> [!TIP]
> Por predefinição quando ativar o serviço de aprovisionamento, irá iniciar aprovisionamento operações para todos os utilizadores no âmbito. Se houver erros nas edições de dados de mapeamento ou Workday, a tarefa de aprovisionamento poderá falhar e vá para o estado de quarentena. Para evitar isto, como melhor prática, recomendamos configurar o **âmbito de objeto de origem** filtro e de teste de seus mapeamentos de atributos com poucos utilizadores de teste antes de iniciar a sincronização completa para todos os utilizadores. Assim que tiver verificado que os mapeamentos de trabalho e dando-lhe os resultados pretendidos, em seguida, pode remover o filtro ou gradualmente expandi-la para incluir mais usuários.

1. Na **aprovisionamento** separador, defina o **estado de aprovisionamento** para **no**.

2. Clique em **Guardar**.

3. Esta operação irá iniciar a sincronização inicial, o que pode demorar um número variável de horas, consoante o número de utilizadores está no inquilino do Workday. 

4. Em qualquer altura, verifique os **registos de auditoria** separador no portal do Azure para ver as ações que efetuou o serviço de aprovisionamento. Os registos de auditoria apresenta uma lista de todos os eventos de sincronização individuais realizados pelo serviço de aprovisionamento, por exemplo, o que os utilizadores estão a ser lido de dia de trabalho e, em seguida, em seguida, adicionados ou atualizados para o Active Directory. Consulte a secção de resolução de problemas para obter instruções sobre como rever os registos de auditoria e corrigir erros de aprovisionamento.

5. Depois de concluída a sincronização inicial, ele irá escrever um relatório de resumo de auditoria no **aprovisionamento** separador, conforme mostrado abaixo.

   ![Portal do Azure](./media/workday-inbound-tutorial/wd_3.png)

## <a name="frequently-asked-questions-faq"></a>Perguntas Mais Frequentes (FAQ)

* **Perguntas de capacidade de solução**
  * [Ao processar uma nova contratação do Workday, como a solução de definir a palavra-passe para a nova conta de utilizador no Active Directory?](#when-processing-a-new-hire-from-workday-how-does-the-solution-set-the-password-for-the-new-user-account-in-active-directory)
  * [A solução suporta o envio de notificações de e-mail após o aprovisionamento completo de operações?](#does-the-solution-support-sending-email-notifications-after-provisioning-operations-complete)
  * [Como gerenciar a entrega de palavras-passe para novas contratações e fornecem um mecanismo para repor a palavra-passe com segurança?](#how-do-i-manage-delivery-of-passwords-for-new-hires-and-securely-provide-a-mechanism-to-reset-their-password)
  * [A solução colocar cache perfis de utilizador do dia de trabalho na cloud do Azure AD ou na camada de agente de aprovisionamento?](#does-the-solution-cache-workday-user-profiles-in-the-azure-ad-cloud-or-at-the-provisioning-agent-layer)
  * [A atribuição de suporte de solução no local grupos AD para o utilizador?](#does-the-solution-support-assigning-on-premises-ad-groups-to-the-user)
  * [As APIs do Workday a solução de utilizar a consulta e atualização de perfis de trabalho do Workday?](#which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles)
  * [Pode configurar meu inquilino do Workday HCM com dois inquilinos do Azure AD?](#can-i-configure-my-workday-hcm-tenant-with-two-azure-ad-tenants)
  * [Por que aplicação de aprovisionamento de utilizadores de "Workday para o Azure AD" não é suportada se podemos ter implementado o Azure AD Connect?](#why-workday-to-azure-ad-user-provisioning-app-is-not-supported-if-we-have-deployed-azure-ad-connect)
  * [Como sugerir melhorias ou pedir novas funcionalidades relacionadas com a integração do Workday e o Azure AD?](#how-do-i-suggest-improvements-or-request-new-features-related-to-workday-and-azure-ad-integration)

* **Perguntas de agente de aprovisionamento**
  * [O que é a versão de disponibilidade geral do agente de aprovisionamento?](#what-is-the-ga-version-of-the-provisioning-agent)
  * [Como posso saber a versão do meu agente de aprovisionamento?](#how-do-i-know-the-version-of-my-provisioning-agent)
  * [Microsoft automaticamente push as atualizações do agente de aprovisionamento?](#does-microsoft-automatically-push-provisioning-agent-updates)
  * [Pode instalar o agente de aprovisionamento no mesmo servidor com o AAD Connect?](#can-i-install-the-provisioning-agent-on-the-same-server-running-aad-connect)
  * [Como posso configurar o agente de aprovisionamento de mensagens em fila a utilizar um servidor proxy para comunicação de HTTP de saída?](#how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication)
  * [Como garantir que o agente de aprovisionamento é capaz de comunicar com o inquilino do Azure AD e não existem firewalls estão a bloquear portas exigidas pelo agente?](#how-do-i-ensure-that-the-provisioning-agent-is-able-to-communicate-with-the-azure-ad-tenant-and-no-firewalls-are-blocking-ports-required-by-the-agent)
  * [Como anular registo do domínio associado meu agente de aprovisionamento?](#how-do-i-de-register-the-domain-associated-with-my-provisioning-agent)
  * [Como posso desinstalar o agente de aprovisionamento?](#how-do-i-uninstall-the-provisioning-agent)
  
* **Dia de trabalho para as perguntas de mapeamento e a configuração do atributo de AD**
  * [Como criar cópias de segurança ou exportar uma cópia de trabalho do meu esquema e de mapeamento do atributo aprovisionamento Workday?](#how-do-i-back-up-or-export-a-working-copy-of-my-workday-provisioning-attribute-mapping-and-schema)
  * [Tenho de atributos personalizados no Workday e o Active Directory. Como posso configurar a solução para funcionar com meu atributos personalizados?](#i-have-custom-attributes-in-workday-and-active-directory-how-do-i-configure-the-solution-to-work-with-my-custom-attributes)
  * [Pode aprovisionar fotografia do utilizador do Workday para o Active Directory?](#can-i-provision-users-photo-from-workday-to-active-directory)
  * [Como posso sincronizar números de telemóvel do Workday com base no consentimento do utilizador para uso público?](#how-do-i-sync-mobile-numbers-from-workday-based-on-user-consent-for-public-usage)
  * [Como eu formate os nomes a apresentar no AD com base em atributos de departamento/país/cidade e variações regionais do identificador do usuário?](#how-do-i-format-display-names-in-ad-based-on-the-users-departmentcountrycity-attributes-and-handle-regional-variances)
  * [Como posso utilizar SelectUniqueValue para gerar valores exclusivos para o atributo samAccountName?](#how-can-i-use-selectuniquevalue-to-generate-unique-values-for-samaccountname-attribute)
  * [Como remover os carateres com diacríticos e convertê-los em letras do alfabeto inglês normais?](#how-do-i-remove-characters-with-diacritics-and-convert-them-into-normal-english-alphabets)

### <a name="solution-capability-questions"></a>Perguntas de capacidade de solução

#### <a name="when-processing-a-new-hire-from-workday-how-does-the-solution-set-the-password-for-the-new-user-account-in-active-directory"></a>Ao processar uma nova contratação do Workday, como a solução de definir a palavra-passe para a nova conta de utilizador no Active Directory?

Quando no local o agente de aprovisionamento obtém um pedido para cria uma nova conta do AD, é automaticamente gera uma palavra-passe aleatória complexa concebida para cumprir os requisitos de complexidade de palavra-passe definidos pelo servidor do AD e define isto no objeto user. Esta palavra-passe não é registado em qualquer lugar.

#### <a name="does-the-solution-support-sending-email-notifications-after-provisioning-operations-complete"></a>A solução suporta o envio de notificações de e-mail após o aprovisionamento completo de operações?

Não, enviar notificações por e-mail após a conclusão de operações de aprovisionamento não é suportada na versão atual.

#### <a name="how-do-i-manage-delivery-of-passwords-for-new-hires-and-securely-provide-a-mechanism-to-reset-their-password"></a>Como gerenciar a entrega de palavras-passe para novas contratações e fornecem um mecanismo para repor a palavra-passe com segurança?

Uma das etapas finais de aprovisionamento da conta de AD nova é a entrega de palavra-passe temporária atribuído à conta do AD do utilizador. Muitas empresas ainda usam a abordagem tradicional de fornecer a palavra-passe temporária para o Gestor do utilizador, que, em seguida, passa a palavra-passe para a nova contratação/contingente. Este processo tem uma falha de segurança inerente e há uma opção disponível para implementar uma abordagem melhor usando os recursos do Azure AD.

Como parte do processo de contratação, as equipes de RH, normalmente, execute uma verificação de segundo plano e verificar o número de celular de nova contratação. Com o Workday à integração de aprovisionamento de utilizador do AD, pode se basear nesse fato e funcionalidade para o utilizador no dia 1 de reposição de implementação de uma palavra-passe self-service. Isto é conseguido ao propagar o atributo "Mobile número" de nova contratação do Workday para o AD e, em seguida, a partir de AD para o Azure AD com o AAD Connect. Depois do "número de Mobile" está presente no Azure AD, pode ativar a [reposição de palavra-passe Self-Service (SSPR)](../authentication/howto-sspr-authenticationdata.md) para a conta de utilizador, por isso, no dia 1, uma nova contratação pode utilizar o número de telemóvel registado e verificado para autenticação.

#### <a name="does-the-solution-cache-workday-user-profiles-in-the-azure-ad-cloud-or-at-the-provisioning-agent-layer"></a>A solução colocar cache perfis de utilizador do dia de trabalho na cloud do Azure AD ou na camada de agente de aprovisionamento?

Não, a solução não mantém uma cache de perfis de utilizador. O serviço de aprovisionamento do Azure AD simplesmente age como um processador de dados, a leitura de dados a partir do Workday e escrever para o destino do Active Directory ou do Azure AD. Consulte a secção [gerir dados pessoais](#managing-personal-data) para obter mais detalhes relacionados com a retenção de dados e privacidade do utilizador.

#### <a name="does-the-solution-support-assigning-on-premises-ad-groups-to-the-user"></a>A atribuição de suporte de solução no local grupos AD para o utilizador?

Esta funcionalidade não é suportada atualmente. Solução recomendada é implantar um script do PowerShell que consulta o ponto de final do Azure AD Graph API para dados de registo de auditoria e usá-lo para acionar cenários, tais como a atribuição de grupo. Este script do PowerShell pode ser anexado a um agendador de tarefas e podem ser implementado na mesma caixa que executa o agente de aprovisionamento.  

#### <a name="which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles"></a>As APIs do Workday a solução de utilizar a consulta e atualização de perfis de trabalho do Workday?

Atualmente, a solução utiliza as seguintes APIs de dia de trabalho:

* Get_Workers (v21.1) para obter informações de função de trabalho
* Maintain_Contact_Information (v26.1) para a funcionalidade de repetição de escrita de E-Mail de trabalho

#### <a name="can-i-configure-my-workday-hcm-tenant-with-two-azure-ad-tenants"></a>Pode configurar meu inquilino do Workday HCM com dois inquilinos do Azure AD?

Sim, esta configuração é suportada. Aqui estão as etapas de alto nível para configurar este cenário:

* Implementar o agente de aprovisionamento do #1 e registá-lo no inquilino do Azure AD #1.
* Implementar o agente de aprovisionamento do #2 e registá-lo no inquilino do Azure AD #2.
* Com base nos "domínios subordinados" que cada agente de aprovisionamento irá gerir, configure cada agente com os domínios. Um agente pode processar vários domínios.
* No portal do Azure, configuração do Workday para aplicação de aprovisionamento de utilizador do AD em cada inquilino e configurá-lo com os respectivos domínios.

#### <a name="why-workday-to-azure-ad-user-provisioning-app-is-not-supported-if-we-have-deployed-azure-ad-connect"></a>Por que aplicação de aprovisionamento de utilizadores de "Workday para o Azure AD" não é suportada se podemos ter implementado o Azure AD Connect?

Quando o Azure AD é utilizado no modo híbrido (em que ele contém um misto de cloud + os utilizadores no local), é importante ter uma definição clara da "origem de autoridade". Geralmente, os cenários híbridos requerem a implementação do Azure AD Connect. Quando o Azure AD Connect é implementado, no local AD é a origem de autoridade. Apresentando o Workday para o conector do Azure AD para a mistura pode levar a uma situação em que valores de atributo do Workday potencialmente poderiam substituir os valores definidos pelo Azure AD Connect. Por conseguinte, a utilização da aplicação de aprovisionamento "Workday para o Azure AD" não é suportada quando o Azure AD Connect está ativado. Nesse tipo de situação, recomendamos que utilize "Workday para AD User" aprovisionamento de aplicação para obter os utilizadores no AD no local e, em seguida, sincronizando-as para o Azure AD com o Azure AD Connect.

#### <a name="how-do-i-suggest-improvements-or-request-new-features-related-to-workday-and-azure-ad-integration"></a>Como sugerir melhorias ou pedir novas funcionalidades relacionadas com a integração do Workday e o Azure AD?

Seus comentários são muito valorizados, pois isso ajuda-na definir a direção para as versões futuras e melhoramentos. Agradecemos a todos os comentários e encorajamos a enviar a sua sugestão ideia ou uma melhoria na [fórum de comentários do Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory). Para comentários específicos relacionados com a integração do Workday, selecione a categoria *aplicações SaaS* , procure as palavras-chave a utilizar *Workday* encontrar existente feedback relacionado com o Workday.

![Aplicações SaaS de UserVoice](media/workday-inbound-tutorial/uservoice_saas_apps.png)

![Dia de trabalho de UserVoice](media/workday-inbound-tutorial/uservoice_workday_feedback.png)

Quando sugerir uma nova ideia, volte a verificar para ver se alguém já sugeriu uma funcionalidade semelhante. Nesse caso, pode votar até o pedido de funcionalidade ou de aprimoramento. Também pode deixar um comentário em relação a seu caso de uso específico para mostrar o suporte para a idéia e demonstrar como a funcionalidade será valiosa para demasiado.

### <a name="provisioning-agent-questions"></a>Perguntas de agente de aprovisionamento

#### <a name="what-is-the-ga-version-of-the-provisioning-agent"></a>O que é a versão de disponibilidade geral do agente de aprovisionamento?

* A versão de disponibilidade geral do agente de aprovisionamento é 1.1.30 e acima.
* Se a sua versão do agente é inferior a 1.1.30, está a executar a versão de pré-visualização pública e ele será automaticamente atualizado para a versão de DG se o servidor que aloja o agente tem o .NET 4.7.1 tempo de execução.
  * Pode [verificam a versão do .NET](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) instalado no seu servidor. Se o servidor não está em execução .NET 4.7.1, pode [baixe e instale o .NET 4.7.1](https://support.microsoft.com/help/4033342/the-net-framework-4-7-1-offline-installer-for-windows). O agente de aprovisionamento será atualizado automaticamente para a versão de DG depois de instalar o .NET 4.7.1.

#### <a name="how-do-i-know-the-version-of-my-provisioning-agent"></a>Como posso saber a versão do meu agente de aprovisionamento?

* Inicie sessão no servidor do Windows, onde está instalado o agente de aprovisionamento.
* Aceda a **painel de controlo** -> **desinstalar ou alterar um programa** menu
* Procure a versão correspondente para a entrada **agente Microsoft Azure AD Connect de aprovisionamento**

  ![Portal do Azure](./media/workday-inbound-tutorial/pa_version.png)

#### <a name="does-microsoft-automatically-push-provisioning-agent-updates"></a>Microsoft automaticamente push as atualizações do agente de aprovisionamento?

Sim, a Microsoft atualiza automaticamente o agente de aprovisionamento. Pode desativar as atualizações automáticas, parando o serviço do Windows **Atualizador de agente ligar do Microsoft Azure AD**.

#### <a name="can-i-install-the-provisioning-agent-on-the-same-server-running-aad-connect"></a>Pode instalar o agente de aprovisionamento no mesmo servidor com o AAD Connect?

Sim, pode instalar o agente de aprovisionamento no mesmo servidor que executa o AAD Connect.

#### <a name="at-the-time-of-configuration-the-provisioning-agent-prompts-for-azure-ad-admin-credentials-does-the-agent-store-the-credentials-locally-on-the-server"></a>No momento da configuração do agente de aprovisionamento solicita credenciais de administrador do Azure AD. O agente armazena as credenciais localmente no servidor?

Durante a configuração, o agente de aprovisionamento solicita credenciais de administrador do Azure AD apenas ligar ao seu inquilino do Azure AD. Ele não armazena as credenciais localmente no servidor. No entanto, manter as credenciais utilizadas para ligar à *domínio do Active Directory no local* num cofre de palavras-passe do Windows local.

#### <a name="how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication"></a>Como posso configurar o agente de aprovisionamento de mensagens em fila a utilizar um servidor proxy para comunicação de HTTP de saída?

O agente de aprovisionamento suporta a utilização de proxy de saída. Pode configurá-lo ao editar o ficheiro de configuração do agente **AD de C:\Program Files\Microsoft Azure-ligar Agent\AADConnectProvisioningAgent.exe.config aprovisionamento**. Adicione as seguintes linhas na mesma, até o final do arquivo apenas antes do fechar `</configuration>` marca.
Substitua as variáveis [servidor proxy] e [porta de proxy] com o nome do servidor proxy e valores de porta.

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

#### <a name="how-do-i-ensure-that-the-provisioning-agent-is-able-to-communicate-with-the-azure-ad-tenant-and-no-firewalls-are-blocking-ports-required-by-the-agent"></a>Como garantir que o agente de aprovisionamento é capaz de comunicar com o inquilino do Azure AD e não existem firewalls estão a bloquear portas exigidas pelo agente?

Também pode verificar se tem todas as portas necessárias abrir abrindo o [ferramenta de teste de portas de conector](https://aadap-portcheck.connectorporttest.msappproxy.net/) da sua rede no local. Mais marcas de verificação verde significa maior resiliência.

Para certificar-se de que a ferramenta dá-lhe os resultados certos, certifique-se de que para:

* Abra a ferramenta num browser a partir do servidor onde instalou o agente de aprovisionamento.
* Certifique-se de que quaisquer proxies ou firewalls aplicáveis ao seu agente de aprovisionamento também são aplicadas a esta página. Isso pode ser feito no Internet Explorer, acedendo a **definições -> Opções da Internet -> ligações -> definições de LAN**. Nesta página, verá o campo "Utilização de Proxy de servidor para sua LAN". Selecione esta caixa e colocar o endereço de proxy no campo "Address".

#### <a name="can-one-provisioning-agent-be-configured-to-provision-multiple-ad-domains"></a>Um agente de aprovisionamento pode ser configurado para aprovisionar vários domínios de AD?

Sim, um agente de aprovisionamento pode ser configurado para lidar com vários domínios de AD, desde que o agente tem linha Visual para os controladores do respetivo domínio. A Microsoft recomenda a configuração de um grupo de 3 aprovisionamento agentes que servem o mesmo conjunto de domínios de AD para garantir a elevada disponibilidade e fornecer falhar ao longo de suporte.

#### <a name="how-do-i-de-register-the-domain-associated-with-my-provisioning-agent"></a>Como anular registo do domínio associado meu agente de aprovisionamento?

* A partir do portal do Azure, recebe o *ID de inquilino* do seu inquilino do Azure AD.
* Inicie sessão no servidor do Windows que executa o agente de aprovisionamento.
* Abra o powershell como administrador do Windows.
* Altere o diretório que contém os scripts de registo e execute os seguintes comandos, substituindo os \[ID de inquilino\] parâmetro com o valor do seu ID de inquilino.

  ```powershell
  cd “C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\RegistrationPowershell\Modules\PSModulesFolder”
  Import-Module "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\RegistrationPowershell\Modules\PSModulesFolder\AppProxyPSModule.psd1"
  Get-PublishedResources -TenantId "[tenant ID]"
  ```

* Na lista de agentes que aparecem – copiar o valor de "id" campo desse recurso cujos *resourceName* é igual a para o seu nome de domínio do AD.
* Cole o id para este comando e executá-lo no Powershell.

  ```powershell
  Remove-PublishedResource -ResourceId "[resource ID]" -TenantId "[tenant ID]"
  ```

* Executar novamente o Assistente de configuração do agente.
* Quaisquer outros agentes que foram atribuídos anteriormente a este domínio tem de ser reconfiguradas.

#### <a name="how-do-i-uninstall-the-provisioning-agent"></a>Como posso desinstalar o agente de aprovisionamento?

* Inicie sessão no servidor do Windows, onde está instalado o agente de aprovisionamento.
* Aceda a **painel de controlo** -> **desinstalar ou alterar um programa** menu
* Desinstale os seguintes programas:
  * Agente de aprovisionamento do Microsoft Azure AD Connect
  * Atualizador de agente do Microsoft Azure AD Connect
  * Pacote de agente de aprovisionamento do Microsoft Azure AD Connect

### <a name="workday-to-ad-attribute-mapping-and-configuration-questions"></a>Dia de trabalho para as perguntas de mapeamento e a configuração do atributo de AD

#### <a name="how-do-i-back-up-or-export-a-working-copy-of-my-workday-provisioning-attribute-mapping-and-schema"></a>Como criar cópias de segurança ou exportar uma cópia de trabalho do meu esquema e de mapeamento do atributo aprovisionamento Workday?

Pode utilizar o Microsoft Graph API para exportar a configuração de aprovisionamento de utilizadores do Workday. Consulte os passos na secção [exportar e importar a configuração do Workday aprovisionamento atributo mapeamento de utilizadores](#exporting-and-importing-your-configuration) para obter detalhes.

#### <a name="i-have-custom-attributes-in-workday-and-active-directory-how-do-i-configure-the-solution-to-work-with-my-custom-attributes"></a>Tenho de atributos personalizados no Workday e o Active Directory. Como posso configurar a solução para funcionar com meu atributos personalizados?

A solução suporta os atributos personalizados de dia de trabalho e o Active Directory. Para adicionar seus atributos personalizados para o esquema de mapeamento, abra a **mapeamento do atributo** painel e desloque-se para baixo para expandir a secção **Mostrar opções avançadas**. 

![Editar Lista de Atributos](./media/workday-inbound-tutorial/wd_edit_attr_list.png)

Para adicionar seus atributos personalizados do Workday, selecione a opção *lista de atributos de edição para Workday* e para adicionar os atributos de AD personalizados, selecione a opção *Editar lista de atributos no local do Active Directory*.

Veja também:

* [Personalizar a lista de atributos de utilizador do Workday](#customizing-the-list-of-workday-user-attributes)

#### <a name="how-do-i-configure-the-solution-to-only-update-attributes-in-ad-based-on-workday-changes-and-not-create-any-new-ad-accounts"></a>Como posso configurar a solução para atualizar apenas os atributos no AD com base nas alterações de dia de trabalho e cria as contas novas do AD?

Esta configuração pode ser alcançada ao definir o **ações do objeto de destino** no **mapeamentos de atributos** painel, conforme mostrado abaixo:

![Atualizar ação](./media/workday-inbound-tutorial/wd_target_update_only.png)

Selecione a caixa de verificação "Atualizar" para operações de atualização para o fluxo a partir do Workday para o AD. 

#### <a name="can-i-provision-users-photo-from-workday-to-active-directory"></a>Pode aprovisionar fotografia do utilizador do Workday para o Active Directory?

A solução atualmente não suporta a definição atributos binários, tal como *thumbnailPhoto* e *jpegPhoto* no Active Directory.

#### <a name="how-do-i-sync-mobile-numbers-from-workday-based-on-user-consent-for-public-usage"></a>Como posso sincronizar números de telemóvel do Workday com base no consentimento do utilizador para uso público?

* Aceda a "aprovisionamento" painel da sua aplicação de aprovisionamento do Workday.
* Clique em mapeamentos de atributos 
* Sob **mapeamentos**, selecione **sincronizar trabalhadores do Workday no local do Active Directory** (ou **sincronizar trabalhadores do Workday para o Azure AD**).
* Na página de mapeamentos de atributos, desloque para baixo e marque a caixa "Mostrar opções avançadas".  Clique em **Editar lista de atributos para Workday**
* No painel que se abre, localize o atributo "Móvel" e clique na linha, para que pode editar a **expressão de API** ![Mobile com o GDPR](./media/workday-inbound-tutorial/mobile_gdpr.png)

* Substitua a **expressão de API** com a seguinte expressão de novo, que obtém o número de celular de trabalho apenas se o "Sinalizador utilização pública" está definido como "True" no Workday.

    ```
     wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Contact_Data/wd:Phone_Data[translate(string(wd:Phone_Device_Type_Reference/@wd:Descriptor),'abcdefghijklmnopqrstuvwxyz','ABCDEFGHIJKLMNOPQRSTUVWXYZ')='MOBILE' and translate(string(wd:Usage_Data/wd:Type_Data/wd:Type_Reference/@wd:Descriptor),'abcdefghijklmnopqrstuvwxyz','ABCDEFGHIJKLMNOPQRSTUVWXYZ')='WORK' and string(wd:Usage_Data/@wd:Public)='1']/@wd:Formatted_Phone
    ```

* Guarde a lista de atributos.
* Guarde o mapeamento do atributo.
* Limpe o estado atual e reiniciar a sincronização completa.

#### <a name="how-do-i-format-display-names-in-ad-based-on-the-users-departmentcountrycity-attributes-and-handle-regional-variances"></a>Como eu formate os nomes a apresentar no AD com base em atributos de departamento/país/cidade e variações regionais do identificador do usuário?

É um requisito comum para configurar o *displayName* de atributos no AD para que ele também fornece informações sobre o departamento do usuário e o país. Para por exemplo, se John Smith trabalha no departamento de Marketing nos EUA, talvez tenha seu *displayName* ser apresentado como *Smith, John (Marketing-US)*.

Eis como pode lidar com tais requisitos para construir *CN* ou *displayName* para incluir atributos como empresa, unidade de negócios, cidade ou país.

* Cada atributo de dia de trabalho é recuperado usando uma expressão XPATH API subjacente, que pode ser configurada no **mapeamento do atributo -> Avançadas secção -> lista de atributos de edição para Workday**. Segue-se a expressão de XPATH API padrão para Workday *PreferredFirstName*, *PreferredLastName*, *empresa* e *SupervisoryOrganization* atributos.

     [!div class="mx-tdCol2BreakAll"]
     | Atributo de workday | Expressão XPATH de API |
     | ----------------- | -------------------- |
     | PreferredFirstName | WD:Worker/WD:Worker_Data/WD:Personal_Data/WD:Name_Data/WD:Preferred_Name_Data/WD:Name_Detail_Data/WD:First_Name/Text() |
     | PreferredLastName | WD:Worker/WD:Worker_Data/WD:Personal_Data/WD:Name_Data/WD:Preferred_Name_Data/WD:Name_Detail_Data/WD:Last_Name/Text() |
     | Empresa | wd:Worker/wd:Worker_Data/wd:Organization_Data/wd:Worker_Organization_Data[wd:Organization_Data/wd:Organization_Type_Reference/wd:ID[@wd:type='Organization_Type_ID']='Company']/wd:Organization_Reference/@wd:Descriptor |
     | SupervisoryOrganization | WD:Worker / wd:Worker_Data / wd:Organization_Data / wd:Worker_Organization_Data / wd:Organization_Data [wd:Organization_Type_Reference / wd:ID [@wd:type= "Organization_Type_ID"] = 'Supervisão'] /wd:Organization_Name/text() |
  
   Certifique-se com a sua equipa de dia de trabalho que a expressão de API acima é válida para a sua configuração de inquilino do Workday. Se necessário, pode editá-los conforme descrito na secção [personalizar a lista de atributos de utilizador do Workday](#customizing-the-list-of-workday-user-attributes).

* Da mesma forma é obter as informações de país presentes no Workday usando o XPATH seguinte: *wd:Worker / wd:Worker_Data / wd:Employment_Data / wd:Position_Data / wd:Business_Site_Summary_Data / wd:Address_Data / wd:Country_Reference*

     Existem 5 atributos relacionados com o país que estão disponíveis na secção de lista de atributos de dia de trabalho.

     | Atributo de workday | Expressão XPATH de API |
     | ----------------- | -------------------- |
     | CountryReference | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/wd:ID[@wd:type='ISO_3166-1_Alpha-3_Code']/text() |
     | CountryReferenceFriendly | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/@wd:Descriptor |
     | CountryReferenceNumeric | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/wd:ID[@wd:type='ISO_3166-1_Numeric-3_Code']/text() |
     | CountryReferenceTwoLetter | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/wd:ID[@wd:type='ISO_3166-1_Alpha-2_Code']/text() |
     | CountryRegionReference | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Region_Reference/@wd:Descriptor |

  Confirme com a sua equipa de dia de trabalho que as expressões de API acima são válidas para a sua configuração de inquilino do Workday. Se necessário, pode editá-los conforme descrito na secção [personalizar a lista de atributos de utilizador do Workday](#customizing-the-list-of-workday-user-attributes).

* Para construir a expressão de mapeamento do atributo certo, identifique o atributo que Workday "forma autoritativa" representa o utilizador nome próprio, último nome, o país e departamento. Vamos supor que os atributos são *PreferredFirstName*, *PreferredLastName*, *CountryReferenceTwoLetter* e *SupervisoryOrganization* , respetivamente. Pode utilizá-lo para criar uma expressão do AD *displayName* atributo da seguinte forma para obter um nome a apresentar como *Smith, John (Marketing-US)*.

    ```
     Append(Join(", ",[PreferredLastName],[PreferredFirstName]), Join(""," (",[SupervisoryOrganization],"-",[CountryReferenceTwoLetter],")"))
    ```
    Assim que tiver a expressão da direita, edite a tabela de mapeamentos de atributos e modificar os *displayName* mapeamento do atributo conforme mostrado abaixo:   ![Mapeamento de DisplayName](./media/workday-inbound-tutorial/wd_displayname_map.png)

* Estendendo o exemplo acima, vamos dizer que pretende converter os nomes das cidades chegam a partir do Workday valores de um atalho e, em seguida, utilizá-lo a criar nomes a apresentar, tal como *Smith, John (CHI)* ou *Martins, a Joana (NYC)*, em seguida, este resultado pode ser obtido usando uma expressão de comutador com o Workday *município* atributo como a variável determinante.

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
  * [Sintaxe da função de comutador](../manage-apps/functions-for-customizing-application-data.md#switch)
  * [Junte-se a sintaxe da função](../manage-apps/functions-for-customizing-application-data.md#join)
  * [Acrescentar a sintaxe da função](../manage-apps/functions-for-customizing-application-data.md#append)

#### <a name="how-can-i-use-selectuniquevalue-to-generate-unique-values-for-samaccountname-attribute"></a>Como posso utilizar SelectUniqueValue para gerar valores exclusivos para o atributo samAccountName?

Digamos que pretende gerar valores exclusivos para *samAccountName* usando uma combinação de atributo *FirstName* e *LastName* atributos a partir do Workday. Tendo em conta abaixo é uma expressão que pode começar com:

```
SelectUniqueValue(
    Replace(Mid(Replace(NormalizeDiacritics(StripSpaces(Join("",  Mid([FirstName],1,1), [LastName]))), , "([\\/\\\\\\[\\]\\:\\;\\|\\=\\,\\+\\*\\?\\<\\>])", , "", , ), 1, 20), , "(\\.)*$", , "", , ),
    Replace(Mid(Replace(NormalizeDiacritics(StripSpaces(Join("",  Mid([FirstName],1,2), [LastName]))), , "([\\/\\\\\\[\\]\\:\\;\\|\\=\\,\\+\\*\\?\\<\\>])", , "", , ), 1, 20), , "(\\.)*$", , "", , ),
    Replace(Mid(Replace(NormalizeDiacritics(StripSpaces(Join("",  Mid([FirstName],1,3), [LastName]))), , "([\\/\\\\\\[\\]\\:\\;\\|\\=\\,\\+\\*\\?\\<\\>])", , "", , ), 1, 20), , "(\\.)*$", , "", , ),
)
```

Como funciona a expressão acima: Se o utilizador for John Smith, primeiro ele tenta gerar JSmith, se já existir JSmith, em seguida, ele gera JoSmith, se de que existe, gera JohSmith. A expressão também garante que o valor gerado satisfaz a restrição de comprimento e a restrição de carateres especiais associadas *samAccountName*.

Veja também:

* [Mid sintaxe da função](../manage-apps/functions-for-customizing-application-data.md#mid)
* [Substitua a sintaxe da função](../manage-apps/functions-for-customizing-application-data.md#replace)
* [Sintaxe da função de SelectUniqueValue](../manage-apps/functions-for-customizing-application-data.md#selectuniquevalue)

#### <a name="how-do-i-remove-characters-with-diacritics-and-convert-them-into-normal-english-alphabets"></a>Como remover os carateres com diacríticos e convertê-los em letras do alfabeto inglês normais?

Utilize a função [NormalizeDiacritics](../manage-apps/functions-for-customizing-application-data.md#normalizediacritics) para remover os carateres especiais no nome próprio e apelido do utilizador, ao construir o endereço de e-mail ou CN valor para o utilizador.

## <a name="troubleshooting-tips"></a>Sugestões de resolução de problemas

Esta seção fornece documentação de orientação específica sobre como resolver problemas de aprovisionamento problemas com a sua integração do Workday através dos registos de auditoria do Azure AD e os registos do Visualizador de eventos do Windows Server. Ele cria sobre os passos de resolução de problemas genéricos e conceitos capturado no [Tutorial: Relatórios sobre o aprovisionamento de contas de utilizadores automático](../manage-apps/check-status-user-account-provisioning.md)

Esta secção abrange os seguintes aspectos de resolução de problemas:

* [Configurar o Visualizador de eventos do Windows para resolução de problemas de agente](#setting-up-windows-event-viewer-for-agent-troubleshooting)
* [Configurar o portal do Azure registos de auditoria para a resolução de problemas de serviço](#setting-up-azure-portal-audit-logs-for-service-troubleshooting)
* [Operações de criação de compreender os registos para a conta de utilizador do AD](#understanding-logs-for-ad-user-account-create-operations)
* [Compreender os registos de Gestor de operações de atualização](#understanding-logs-for-manager-update-operations)
* [Resolução comumente encontrou erros](#resolving-commonly-encountered-errors)

### <a name="setting-up-windows-event-viewer-for-agent-troubleshooting"></a>Configurar o Visualizador de eventos do Windows para resolução de problemas de agente

* Inicie sessão na máquina do Windows Server onde o agente de aprovisionamento é implementado
* Open **Visualizador de eventos do Windows Server** aplicativo de desktop.
* Selecione **registos do Windows > aplicação**.
* Utilize o **Filtrar registo atual...** opção para ver todos os eventos registados na origem **AAD. Connect.ProvisioningAgent** e exclui eventos com o ID de evento "5", especificando o filtro "-5", conforme mostrado abaixo.

  ![Visualizador de eventos do Windows](media/workday-inbound-tutorial/wd_event_viewer_01.png))

* Clique em **OK** e a classificação da exibição de resultado por **data e hora** coluna.

### <a name="setting-up-azure-portal-audit-logs-for-service-troubleshooting"></a>Configurar o portal do Azure registos de auditoria para a resolução de problemas de serviço

* Iniciar o [portal do Azure](https://portal.azure.com)e navegue para o **registos de auditoria** secção do seu dia de trabalho a aplicação de aprovisionamento.
* Utilize o **colunas** botão na página de registos de auditoria para apresentar apenas as seguintes colunas na vista de (data, atividade, estado, razão do Estado). Esta configuração garante que se concentre apenas nos dados que são relevantes para resolução de problemas.

  ![Colunas de log de auditoria](media/workday-inbound-tutorial/wd_audit_logs_00.png)

* Utilize o **destino** e **intervalo de datas** parâmetros para filtrar a vista de consulta. 
  * Definir o **destino** consultar o parâmetro para o "ID da função de trabalho" ou "ID de funcionário" do objeto de trabalho do Workday.
  * Definir o **intervalo de datas** para um período de tempo adequado através do qual pretende investigar para erros ou problemas com o aprovisionamento.

  ![Filtros de registo de auditoria](media/workday-inbound-tutorial/wd_audit_logs_01.png)

### <a name="understanding-logs-for-ad-user-account-create-operations"></a>Operações de criação de compreender os registos para a conta de utilizador do AD

Quando é detetada uma nova contratação no Workday (Digamos, com o ID de funcionário *21023*), o Azure AD tentativas de serviço para criar uma nova conta de utilizador do AD para a função de trabalho e no processo de aprovisionamento cria 4 registros de log de auditoria, conforme descrito abaixo:

  [![Registo de auditoria criar ops](media/workday-inbound-tutorial/wd_audit_logs_02.png)](media/workday-inbound-tutorial/wd_audit_logs_02.png#lightbox)

Quando clica em qualquer um dos registros de log de auditoria, o **detalhes de atividade** abrirá a página. Eis o que o **detalhes de atividade** página é apresentada para cada tipo de registo do registo.

* **Importar workday** registo: Este registo apresenta as informações de função de trabalho obtidas a partir do Workday. Utilizar informações a *detalhes adicionais* secção do registo de registo para resolver problemas com a obter dados a partir do Workday. Um registo de exemplo é mostrado abaixo, juntamente com apontadores sobre como interpretar a cada campo.

  ```JSON
  ErrorCode : None  // Use the error code captured here to troubleshoot Workday issues
  EventName : EntryImportAdd // For full sync, value is "EntryImportAdd" and for delta sync, value is "EntryImport"
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID (usually the Worker ID or Employee ID field)
  SourceAnchor : a071861412de4c2486eb10e5ae0834c3 // set to the WorkdayID (WID) associated with the record
  ```

* **Importação de AD** registo: Este registo apresenta informações da conta obtida a partir do AD. Uma vez durante a criação do utilizador inicial, não há nenhuma conta do AD, o *razão do Estado de atividade* indicará que não existe nenhuma conta com o valor do atributo de ID de correspondência foi encontrada no Active Directory. Utilizar informações a *detalhes adicionais* secção do registo de registo para resolver problemas com a obter dados a partir do Workday. Um registo de exemplo é mostrado abaixo, juntamente com apontadores sobre como interpretar a cada campo.

  ```JSON
  ErrorCode : None // Use the error code captured here to troubleshoot Workday issues
  EventName : EntryImportObjectNotFound // Implies that object was not found in AD
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID
  ```

  Para localizar os registros de log de agente de aprovisionamento correspondente a esta operação de importação do AD, abra os registos do Visualizador de eventos do Windows e utilizar o **localizar...** opção de menu para encontrar entradas de registo que contém o valor do atributo de propriedade de ID/Joining correspondente (nesse caso *21023*).

  ![Localizar](media/workday-inbound-tutorial/wd_event_viewer_02.png)

  Procurar a entrada com *ID de evento = 9*, que fornecerá a o LDAP utilizado pelo agente para obter a conta AD de filtro de pesquisa. Pode verificar se este é o filtro de pesquisa direito a obter entradas de utilizador exclusivo.

  ![Pesquisa LDAP](media/workday-inbound-tutorial/wd_event_viewer_03.png)

  O registo que segue imediatamente com *ID de evento = 2* captura o resultado da operação de pesquisa e se ele retornasse quaisquer resultados.

  ![Resultados LDAP](media/workday-inbound-tutorial/wd_event_viewer_04.png)

* **Ação de regra de sincronização** registo: Este registo apresenta os resultados das regras de mapeamento de atributo e configurado filtros de âmbito, juntamente com a ação de aprovisionamento que serão tomadas para processar o evento de entrada Workday. Utilizar informações a *detalhes adicionais* secção do registo de registo para resolver problemas com a ação de sincronização. Um registo de exemplo é mostrado abaixo, juntamente com apontadores sobre como interpretar a cada campo.

  ```JSON
  ErrorCode : None // Use the error code captured here to troubleshoot sync issues
  EventName : EntrySynchronizationAdd // Implies that the object will be added
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID
  SourceAnchor : a071861412de4c2486eb10e5ae0834c3 // set to the WorkdayID (WID) associated with the profile in Workday
  ```

  Se existirem problemas com as expressões de mapeamento do atributo ou dos dados recebidos do Workday tem problemas (por exemplo: vazio ou valor nulo para atributos obrigatórios), em seguida, irá observar uma falha nesta fase com o código de erro fornecer detalhes da falha.

* **Exportação do AD** registo: Este registo apresenta o resultado da operação de criação de conta AD juntamente com os valores de atributos que foram definidas no processo. Utilizar informações a *detalhes adicionais* secção do registo de registo para resolver problemas com a conta de operação de criação. Um registo de exemplo é mostrado abaixo, juntamente com apontadores sobre como interpretar a cada campo. Na secção "Detalhes adicionais", "EventName" está definido como "EntryExportAdd", "JoiningProperty" está definido como o valor do atributo de ID de correspondência, "SourceAnchor" está definido para o WorkdayID (WID) associados com o registo e o "TargetAnchor" está definido para o valor do AD "ObjectGuid" atributo do utilizador recém-criado. 

  ```JSON
  ErrorCode : None // Use the error code captured here to troubleshoot AD account creation issues
  EventName : EntryExportAdd // Implies that object will be created
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID
  SourceAnchor : a071861412de4c2486eb10e5ae0834c3 // set to the WorkdayID (WID) associated with the profile in Workday
  TargetAnchor : 83f0156c-3222-407e-939c-56677831d525 // set to the value of the AD "objectGuid" attribute of the new user
  ```

  Para localizar os registros de log de agente de aprovisionamento correspondente a esta operação de exportação do AD, abra os registos do Visualizador de eventos do Windows e utilizar o **localizar...** opção de menu para encontrar entradas de registo que contém o valor do atributo de propriedade de ID/Joining correspondente (nesse caso *21023*).  

  Procurar um registo de HTTP POST correspondente para o período de tempo de operação de exportação com *ID de evento = 2*. Este registo conterá os valores de atributo enviados pelo serviço de aprovisionamento para o agente de aprovisionamento.

  [![Adicionar SCIM](media/workday-inbound-tutorial/wd_event_viewer_05.png)](media/workday-inbound-tutorial/wd_event_viewer_05.png#lightbox)

  Imediatamente após o evento acima, deverá haver outro evento que captura a resposta da operação de conta AD de create. Este evento retorna o objectGuid nova criado no AD e é definida como o atributo de TargetAnchor no serviço de aprovisionamento.

  [![Adicionar SCIM](media/workday-inbound-tutorial/wd_event_viewer_06.png)](media/workday-inbound-tutorial/wd_event_viewer_06.png#lightbox)

### <a name="understanding-logs-for-manager-update-operations"></a>Compreender os registos de Gestor de operações de atualização

O atributo de gestor é um atributo de referência no AD. O serviço de aprovisionamento não define o atributo de gestor como parte da operação de criação de utilizador. Em vez disso, o atributo de gestor está definido como parte de um *atualizar* operação depois de criar a conta de AD para o utilizador. Expandir o exemplo acima, vamos supor que uma nova contratação com o ID de funcionário "21451" é ativada no dia de trabalho e o Gestor da nova contratação (*21023*) já tem uma conta do AD. Neste cenário, procurar os registos de auditoria para o utilizador 21451 é apresentada 5 entradas.

  [![Atualização do Gestor](media/workday-inbound-tutorial/wd_audit_logs_03.png)](media/workday-inbound-tutorial/wd_audit_logs_03.png#lightbox)

Os primeiros 4 registos são como os que exploramos como parte do utilizador criar a operação. O registo 5th é a exportação associada com a atualização do atributo de gestor. O registro de log apresenta o resultado da operação atualizar do AD conta manager, que é executado usando o Gerenciador *objectGuid* atributo.

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

### <a name="resolving-commonly-encountered-errors"></a>Resolução comumente encontrou erros

Esta secção abrange os erros comuns encontrados com o aprovisionamento de utilizadores do Workday e como resolvê-lo. Os erros são agrupados, da seguinte forma:

* [Erros de agente de aprovisionamento](#provisioning-agent-errors)
* [Erros de conectividade](#connectivity-errors)
* [Erros de criação de conta de utilizador do AD](#ad-user-account-creation-errors)
* [Erros de atualização de conta de utilizador do AD](#ad-user-account-update-errors)

#### <a name="provisioning-agent-errors"></a>Erros de agente de aprovisionamento

|#|Cenário de erro |Causas prováveis|Resolução recomendada|
|--|---|---|---|
|1.| Erro ao instalar o agente de aprovisionamento com a mensagem de erro:  *Serviço "Microsoft agente Azure AD Connect de aprovisionamento" (AADConnectProvisioningAgent) falhou ao iniciar. Certifique-se de que tem privilégios suficientes para iniciar o sistema.* | Este erro, normalmente, é apresentada se estiver a tentar instalar o agente de aprovisionamento num controlador de domínio e diretiva de grupo impede que o serviço seja inicializado.  Também é apresentado se tiver uma versão anterior do agente em execução e não o desinstalou-lo antes de iniciar uma nova instalação.| Instale o agente de aprovisionamento num servidor não DC. Certifique-se de que as versões anteriores do agente são desinstaladas antes de instalar o agente de novo.|
|2.| O serviço Windows "Agente Microsoft Azure AD Connect de aprovisionamento" está em *inicial* de estado e não mudar para *em execução* estado. | Como parte da instalação, o Assistente de agente cria uma conta local (**serviço NT\\AADConnectProvisioningAgent**) no servidor e isso é o **iniciar sessão** conta utilizada para iniciar o serviço. Se uma política de segurança no seu servidor de Windows impede que as contas locais em execução os serviços, irá encontrar este erro. | Abra o *consola de serviços*. Clique com o botão direito do rato no serviço Windows "Agente Microsoft Azure AD Connect de aprovisionamento" e no separador de iniciar sessão, especifique a conta de administrador de domínio para executar o serviço. Reinicie o serviço. |
|3.| Quando configurar o agente de aprovisionamento com o seu domínio do AD no passo *ligar o Active Directory*, o assistente demora muito tempo tentando carregar o esquema do AD e, eventualmente, exceder o tempo limite. | Este erro, normalmente, é apresentada se o assistente não consegue contactar o servidor de controlador de domínio do AD devido a problemas de firewall. | Sobre o *ligue o Active Directory* ecrã do assistente, ao mesmo tempo, as credenciais para o seu domínio do AD, é uma opção chamada *selecione a prioridade de controlador de domínio*. Utilize esta opção para selecionar um controlador de domínio que está no mesmo site que o servidor de agente e certifique-se de que não há nenhuma regra de firewall a bloquear a comunicação. |

#### <a name="connectivity-errors"></a>Erros de conectividade

Se o serviço de aprovisionamento não é possível ligar ao Workday ou Active Directory, pode causar o aprovisionamento entrar num Estado em quarentena. Utilize a tabela abaixo para resolver problemas de conectividade.

|#|Cenário de erro |Causas prováveis|Resolução recomendada|
|--|---|---|---|
|1.| Quando clica em **Testar ligação**, obterá a mensagem de erro: *Ocorreu um erro ao ligar ao Active Directory. Certifique-se de que o agente de aprovisionamento no local está em execução e está configurado com o domínio do Active Directory correto.* | Este erro normalmente aparece se o aprovisionamento de agente não está em execução ou pode existir uma firewall a bloquear a comunicação entre o Azure AD e o agente de aprovisionamento. Também poderá ver este erro, se o domínio não está configurado no Assistente de agente. | Abra o *serviços* consola no servidor do Windows para confirmar que o agente está em execução. Abra o Assistente de aprovisionamento do agente e confirme que o domínio correto está registrado com o agente.  |
|2.| A tarefa de aprovisionamento entra em estado de quarentena ao longo da semana (Sábado sexta-feira) e obtemos uma notificação por e-mail que existe um erro com a sincronização. | Uma das causas comuns deste erro é o tempo de inatividade planeado do Workday. Se estiver a utilizar um inquilino de implementação do Workday, tenha em atenção que o Workday tem agendadas para período de indisponibilidade para os seus inquilinos de implementação a fim de semana (normalmente, a partir de noite de sexta-feira de manhã de Sábado) e durante esse período, o Workday aprovisionamento de aplicações pode entrar no Estado de quarentena, pois não é possível estabelecer ligação com o Workday. Ele recebe de volta ao estado normal assim que o inquilino do Workday implementação esteja novamente online. Em casos raros, também poderá ver este erro, se a palavra-passe do utilizador do sistema de integração alterado devido à atualização do inquilino ou se a conta está no bloqueado ou expirado estado. | Contacte o seu parceiro de administrador ou de integração do Workday para ver quando Workday agendas de tempo de inatividade para ignorar mensagens de alerta durante o período de tempo de inatividade e confirmar a disponibilidade assim que a instância do Workday esteja novamente online.  |


#### <a name="ad-user-account-creation-errors"></a>Erros de criação de conta de utilizador do AD

|#|Cenário de erro |Causas prováveis|Resolução recomendada|
|--|---|---|---|
|1.| Exportar as falhas de operação no log de auditoria com a mensagem *erro: OperationsError-SvcErr: Ocorreu um erro de operação. Nenhuma referência superior foi configurada para o serviço de diretório. O serviço de diretório, por conseguinte, é possível ao problema de referências a objetos fora desta floresta.* | Este erro aparece, normalmente, se o *contentor do Active Directory* UO não está corretamente definido ou se existirem problemas com o mapeamento de expressão utilizada para *parentDistinguishedName*. | Verifique os *contentor do Active Directory* parâmetro de UO para erros de digitação. Se estiver a utilizar *parentDistinguishedName* o mapeamento do atributo Certifique-se de que ela sempre é avaliada como um contêiner conhecido dentro do domínio do AD. Verifique os *exportar* os logs de eventos na auditoria para ver o valor gerado. |
|2.| Exporte falhas de operação no log de auditoria com o código de erro: *SystemForCrossDomainIdentityManagementBadResponse* e a mensagem *erro: ConstraintViolation-AtrErr: Um valor no pedido é inválido. Um valor para o atributo não estava no intervalo aceitável de valores. detalhes de \nError: CONSTRAINT_ATT_TYPE - da empresa*. | Embora este erro é específico para o *empresa* atributo, poderá ver este erro para outros atributos como *CN* também. Este erro ocorre devido a restrição de esquema do AD imposta. Por predefinição, os atributos, como *empresa* e *CN* no AD tem um limite superior de 64 carateres. Se o valor de dia de trabalho for mais de 64 carateres, em seguida, irá ver esta mensagem de erro. | Verifique os *exportar* eventos nos registos de auditoria para ver o valor do atributo reportados na mensagem de erro. Considere truncar o valor provenientes do Workday através da [Mid](../manage-apps/functions-for-customizing-application-data.md#mid) função ou alterar os mapeamentos para um atributo de AD não tem as restrições de tamanho semelhante.  |

#### <a name="ad-user-account-update-errors"></a>Erros de atualização de conta de utilizador do AD

Durante o processo de atualização do AD utilizador conta, o serviço de aprovisionamento, lê as informações do Workday e AD é executado o atributo de regras de mapeamento e determina se qualquer alteração necessidades entrem em vigor. Em conformidade é acionado um evento de atualização. Se qualquer um destes passos detetar uma falha, é registado nos registos de auditoria. Utilize a tabela abaixo para resolver erros comuns de atualização.

|#|Cenário de erro |Causas prováveis|Resolução recomendada|
|--|---|---|---|
|1.| Falhas de ação de regra de sincronização no log de auditoria com a mensagem *EventName = EntrySynchronizationError e código de erro = EndpointUnavailable*. | Este erro aparece se o serviço de aprovisionamento não consegue obter dados de perfil de utilizador do Active Directory devido a um erro de processamento encontrado pela no local o agente de aprovisionamento. | Verifique os registos do Visualizador de eventos de agente de aprovisionamento de eventos de erro indicam problemas com a operação de leitura (Filtrar por ID de evento n º 2). |
|2.| O atributo de gestor no AD não for atualizado para determinados utilizadores no AD. | A causa mais provável deste erro é se estiver a utilizar as regras de escopo e o Gestor do utilizador não é parte do âmbito. Também pode executar este erro se o atributo de ID correspondente (por exemplo, o EmployeeID) do gestor não foi encontrado no domínio do AD de destino ou não definido para o valor correto. | Reveja o filtro de âmbito e adicionar o utilizador do gestor no âmbito. Verifique o perfil do Gestor do AD para certificar-se de que existe um valor para o atributo de ID correspondente. |

## <a name="managing-your-configuration"></a>Gerir a configuração

Esta secção descreve como pode ainda mais expandir, personalizar e gerir o seu utilizador orientada por dia de trabalho, configuração de aprovisionamento. Ele abrange os seguintes tópicos:

* [Personalizar a lista de atributos de utilizador do Workday](#customizing-the-list-of-workday-user-attributes)  
* [Exportar e importar a configuração](#exporting-and-importing-your-configuration)

### <a name="customizing-the-list-of-workday-user-attributes"></a>Personalizar a lista de atributos de utilizador do Workday

O Workday aprovisionamento de aplicações para o Active Directory e o Azure AD incluem uma lista de padrão de atributos de utilizador do Workday pode selecionar. No entanto, essas listas não são abrangentes. Workday oferece suporte a várias centenas de atributos de utilizador possível, o que podem ser exclusivo para o seu inquilino do Workday ou padrão.

O Azure AD que o serviço de aprovisionamento suporta a capacidade de personalizar a sua lista ou um atributo do Workday para incluir todos os atributos expostos no [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) operação da API de recursos humanos.

Para fazer esta alteração, tem de utilizar [Workday Studio](https://community.workday.com/studio-download) para extrair as expressões XPath que representam os atributos que deseja usar e, em seguida, adicioná-los à sua configuração de aprovisionamento com o editor de atributos avançadas no portal do Azure .

**Para obter uma expressão de XPath para um atributo de utilizador do dia de trabalho:**

1. Transfira e instale [Workday Studio](https://community.workday.com/studio-download). Precisará de uma conta de Comunidade do Workday para acessar o instalador.

2. Transfira o ficheiro de Workday Human_Resources WSDL a partir deste URL: https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Human_Resources.wsdl

3. Inicie Workday Studio.

4. Na barra de comandos, selecione o **Workday > serviço Web de teste no Tester** opção.

5. Selecione **externo**e selecione o ficheiro Human_Resources WSDL que transferiu no passo 2.

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio1.png)

6. Definir o **localização** campo `https://IMPL-CC.workday.com/ccx/service/TENANT/Human_Resources`, mas substituindo "IMPL CC" real com tipo de instância e "INQUILINO" com o nome do seu inquilino real.

7. Definir **operação** para **Get_Workers**

8.  Clique em pequenos **configurar** link abaixo os painéis de solicitação/resposta para definir as suas credenciais do Workday. Verifique **autenticação**e, em seguida, introduza o nome de utilizador e palavra-passe para a sua conta de sistema de integração do Workday. Certifique-se de que formatar o nome de utilizador como nome\@inquilino e deixe o **WS-Security UsernameToken** opção selecionada.

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio2.png)

9. Selecione **OK**.

10. Na **pedir** painel, colar no XML abaixo e definido **Employee_ID** para o ID de funcionário de um utilizador real no seu inquilino do Workday. Selecione um utilizador que tem o atributo preenchido que pretende extrair.

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

11. Clique nas **enviar pedido** (seta verde) a executar o comando. Se tiver êxito, a resposta deverá aparecer na **resposta** painel. Verifique a resposta para garantir que tem do ID de utilizador que introduziu os dados e não um erro.

12. Se tiver êxito, copie o XML dos **resposta** painel e guarde-o como um arquivo XML.

13. No comando barra do Workday Studio, selecione **ficheiro > abrir o ficheiro...**  e abra o ficheiro XML que guardou. Esta ação irá abrir o ficheiro no editor de XML do Workday Studio.

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio3.png)

14. Na árvore de arquivos, navegar pelo **/env: Envelope > env: Corpo > wd:Get_Workers_Response > wd:Response_Data > wd: Função de trabalho** para localizar os dados dos seus utilizadores.

15. Em **wd: Função de trabalho**, encontrar o atributo que pretende adicionar e selecione-o.

16. Copie a expressão de XPath para seu atributo selecionado do **caminho do documento** campo.

17. Remover os **/env:Envelope / env:Body / wd:Get_Workers_Response / wd:Response_Data /** prefixo da expressão copiado.

18. Se o último item da expressão de copiado é um nó (exemplo: "/ wd: Birth_Date"), em seguida, anexe **/text()** no final da expressão. Isso não é necessário se o último item é um atributo (exemplo: "/@wd: tipo").

19. O resultado deve ser algo como `wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()`. Este valor é o que serão copiados no portal do Azure.

**Para adicionar o atributo de utilizador do Workday personalizado à sua configuração de aprovisionamento:**

1. Iniciar o [portal do Azure](https://portal.azure.com)e navegue para a secção de aprovisionamento do seu aplicativo, o aprovisionamento de dia de trabalho, conforme descrito anteriormente neste tutorial.

2. Definir **estado de aprovisionamento** ao **desativar**e selecione **guardar**. Este passo ajudará a garantir que as alterações entrarão em vigor apenas quando estiver pronto.

3. Sob **mapeamentos**, selecione **sincronizar trabalhadores do Workday no local do Active Directory** (ou **sincronizar trabalhadores do Workday para o Azure AD**).

4. Desloque-se para a parte inferior do ecrã seguinte e selecione **Mostrar opções avançadas**.

5. Selecione **lista de atributos de edição para Workday**.

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio_aad1.png)

6. Desloque-se para a parte inferior da lista de atributos, onde estão os campos de entrada.

7. Para **nome**, introduza um nome de exibição para seu atributo.

8. Para **tipo**, selecione o tipo de forma adequada ao seu atributo (**cadeia de caracteres** é mais comum).

9. Para **expressão de API**, introduza a expressão XPath que copiou a partir do Workday Studio. Exemplo: `wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()`

10. Selecione **adicionar atributo**.

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio_aad2.png)

11. Selecione **salvar** acima e, em seguida **Sim** na caixa de diálogo. Feche o ecrã de mapeamento do atributo, caso esteja ainda aberto.

12. Novamente no principal **aprovisionamento** separador, selecione **sincronizar trabalhadores do Workday no local do Active Directory** (ou **sincronizar funções de trabalho para o Azure AD**) novamente.

13. Selecione **adicionar novo mapeamento**.

14. O novo atributo deverá agora aparecer na **atributo de origem** lista.

15. Adicione um mapeamento para o novo atributo conforme pretendido.

16. Quando terminar, não se esqueça de definir **estado de aprovisionamento** voltar ao **no** e guardar.

### <a name="exporting-and-importing-your-configuration"></a>Exportar e importar a configuração

Esta secção descreve como utilizar a API do Microsoft Graph e do Graph para exportar seus mapeamentos de atributos de dia de trabalho de aprovisionamento e o esquema para um ficheiro JSON e importe-o para o Azure AD.

#### <a name="step-1-retrieve-your-workday-provisioning-app-service-principal-id-object-id"></a>Passo 1: Obter o seu ID de Principal de serviço de aplicação de aprovisionamento (ID de objeto) Workday

1. Iniciar o [portal do Azure](https://portal.azure.com)e navegue para a secção de propriedades do seu dia de trabalho a aplicação de aprovisionamento.
1. Na secção de propriedades da sua aplicação de aprovisionamento, copie o valor GUID associado a *ID de objeto* campo. Este valor também é denominado o **ServicePrincipalId** da sua aplicação e ele será utilizado em operações de API do Graph.

   ![ID de Principal de serviço de aplicações do dia de trabalho](./media/workday-inbound-tutorial/wd_export_01.png)

#### <a name="step-2-sign-into-microsoft-graph-explorer"></a>Passo 2: Inicie sessão em do Microsoft Graph

1. Iniciar [do Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer)
1. Clique no botão de "Início de sessão com o Microsoft" e inicie sessão com credenciais de Administrador Global do AD ou administrador de aplicações do Azure.

    ![Início de sessão no gráfico](./media/workday-inbound-tutorial/wd_export_02.png)

1. Após o início de sessão com êxito, verá os detalhes da conta de utilizador no painel esquerdo.

#### <a name="step-3-retrieve-the-provisioning-job-id-of-the-workday-provisioning-app"></a>Passo 3: Obter o ID de tarefa de aprovisionamento da aplicação de aprovisionamento Workday

No Microsoft Graph Explorer, execute a seguinte consulta GET substituindo [servicePrincipalId] com o **ServicePrincipalId** extraiu da [passo 1](#step-1-retrieve-your-workday-provisioning-app-service-principal-id-object-id).

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs
```

Obterá uma resposta conforme mostrado abaixo. Copie o atributo"id" presente na resposta. Este valor é o **ProvisioningJobId** e será utilizado para obter os metadados de esquema subjacente.

   [![Id da tarefa de aprovisionamento](./media/workday-inbound-tutorial/wd_export_03.png)](./media/workday-inbound-tutorial/wd_export_03.png#lightbox)

#### <a name="step-4-download-the-provisioning-schema"></a>Passo 4: Transferir o esquema de aprovisionamento

No Microsoft Graph Explorer, execute a seguinte consulta GET, substituindo [servicePrincipalId] e [ProvisioningJobId] com o ServicePrincipalId e o ProvisioningJobId obtidos nos passos anteriores.

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

Copie o objeto JSON da resposta e guarde-o para um ficheiro para criar uma cópia de segurança do esquema.

#### <a name="step-5-import-the-provisioning-schema"></a>Passo 5: Importe o esquema de aprovisionamento

> [!CAUTION]
> Efetue este passo apenas se tiver de modificar o esquema para a configuração não pode ser alterado no portal do Azure ou se tiver de restaurar a configuração de um ficheiro anteriormente cópia de segurança com válido e o esquema de trabalho.

No Microsoft Graph Explorer, configure as seguintes consultas PUT, substituindo [servicePrincipalId] e [ProvisioningJobId] com o ServicePrincipalId e o ProvisioningJobId obtidos nos passos anteriores.

```http
    PUT https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

No separador "Corpo do pedido", copie o conteúdo do ficheiro de esquema JSON.

   [![Corpo do pedido](./media/workday-inbound-tutorial/wd_export_04.png)](./media/workday-inbound-tutorial/wd_export_04.png#lightbox)

No separador "Cabeçalhos de pedido", adicione o atributo de cabeçalho de tipo de conteúdo com o valor "application/json"

   [![Cabeçalhos de pedido](./media/workday-inbound-tutorial/wd_export_05.png)](./media/workday-inbound-tutorial/wd_export_05.png#lightbox)

Clique no botão "Executar consulta" para importar o esquema novo.

## <a name="managing-personal-data"></a>Gerir dados pessoais

O Workday, solução de provisionamento do Active Directory requer um agente de aprovisionamento ser instalado num servidor do Windows no local, e este agente cria registos no log de eventos do Windows que pode conter dados pessoais, dependendo do seu dia de trabalho para o atributo de AD mapeamentos. Para estar em conformidade com obrigações de privacidade do utilizador, pode certificar-se de que não existem dados são retidos no caso de registos para além de 48 horas configurando um Windows agendada de tarefa para limpar o registo de eventos.

O Azure AD que o serviço de aprovisionamento recai sobre o **processador dos dados** categoria de classificação de GDPR. Como um pipeline de processador de dados, o serviço fornece serviços de processamento de dados principais parceiros e consumidores finais. Serviço de aprovisionamento do Azure AD não gera dados de utilizador e não tem independente controle sobre quais os dados pessoais são recolhidos e como são utilizadas. Obtenção de dados, a agregação, análise e relatórios no serviço de aprovisionamento do Azure AD são baseadas em dados de empresariais existentes.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-hybrid-note.md)]

Em relação à retenção de dados, o Azure AD que o serviço de aprovisionamento não gerar relatórios, realizar análises ou fornecer informações mais de 30 dias. Por conseguinte, serviço de aprovisionamento do Azure AD não armazenam, processam ou reter dados mais de 30 dias. Esse design está em conformidade com os regulamentos de GDPR, regulamentos de conformidade de privacidade do Microsoft e políticas de retenção de dados do Azure AD.

## <a name="next-steps"></a>Passos Seguintes

* [Saiba como rever os registos e obter relatórios de atividade de aprovisionamento](../manage-apps/check-status-user-account-provisioning.md)
* [Saiba como configurar o início de sessão único entre o dia de trabalho e o Azure Active Directory](workday-tutorial.md)
* [Saiba como integrar as outras aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [Saiba como utilizar o Microsoft Graph APIs para gerir configurações de aprovisionamento](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)
