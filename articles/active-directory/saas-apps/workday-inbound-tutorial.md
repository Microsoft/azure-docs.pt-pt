---
title: 'Tutorial: Configurar o Dia de Trabalho para o provisionamento automático do utilizador com o Azure Ative Directory / Microsoft Docs'
description: Saiba como configurar o Azure Ative Directory para provisão automática e desa provisionamento de contas de utilizador para o Workday.
services: active-directory
author: cmmdesai
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.topic: article
ms.workload: identity
ms.date: 05/26/2020
ms.author: chmutali
ms.openlocfilehash: e22252ea3e132aee39075d986d7f5a979e14c0a3
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92520239"
---
# <a name="tutorial-configure-workday-for-automatic-user-provisioning"></a>Tutorial: Configurar o Dia de Trabalho para o fornecimento automático de utilizadores

O objetivo deste tutorial é mostrar os passos que você precisa para executar para a provisionar perfis de trabalhadores do Workday para o Ative Directory (AD) no local.

>[!NOTE]
>Utilize este tutorial, se os utilizadores que pretender abastecer a partir do Workday precisam de uma conta AD no local e uma conta AD Azure. 
>* Se os utilizadores do Workday apenas precisarem da conta AZure AD (utilizadores apenas na nuvem), consulte o tutorial no dia de trabalho de configuração para o provisionamento do utilizador [Azure AD.](workday-inbound-cloud-only-tutorial.md) 
>* Para configurar a gravação de atributos como endereço de e-mail, nome de utilizador e número de telefone de Azure AD para Workday, consulte o tutorial sobre configurar a [writeback do Workday](workday-writeback-tutorial.md).


## <a name="overview"></a>Descrição geral

O [serviço de prestação de serviços de atendimento ao utilizador Azure Ative Directory](../app-provisioning/user-provisioning.md) integra-se com a API de Recursos Humanos do [Workday,](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) a fim de prestar contas de utilizadores. O serviço de fornecimento de utilizadores do Workday suportado pelo serviço de fornecimento de utilizadores Azure AD permite automatizar os seguintes recursos humanos e cenários de gestão do ciclo de vida da identidade:

* **Contratação de novos colaboradores** - Quando um novo empregado é adicionado ao Workday, uma conta de utilizador é criada automaticamente em Ative Directory, Azure Ative Directory, e opcionalmente Microsoft 365 e [outras aplicações SaaS suportadas pela Azure AD,](../app-provisioning/user-provisioning.md)com a write-back de informações de contacto geridas por TI para o Workday.

* **Atribuição de colaboradores e atualizações de perfis** - Quando um registo de empregados é atualizado no Workday (como o seu nome, título ou gestor), a sua conta de utilizador será automaticamente atualizada no Ative Directory, no Azure Ative Directory e opcionalmente no Microsoft 365 e [noutras aplicações SaaS suportadas pela Azure AD](../app-provisioning/user-provisioning.md).

* **Rescisões de funcionários** - Quando um empregado é encerrado no Workday, a sua conta de utilizador é automaticamente desativada em Ative Directory, Azure Ative Directory, e opcionalmente Microsoft 365 e [outras aplicações SaaS suportadas pela Azure AD](../app-provisioning/user-provisioning.md).

* **Recontrações de empregados** - Quando um empregado é recontratado no Workday, a sua conta antiga pode ser automaticamente reativada ou re provisionada (dependendo da sua preferência) para o Ative Directory, Azure Ative Directory, e opcionalmente Microsoft 365 e [outras aplicações SaaS apoiadas pela Azure AD](../app-provisioning/user-provisioning.md).

### <a name="whats-new"></a>Novidades
Esta secção captura melhorias recentes da integração do Workday. Para obter uma lista de atualizações completas, alterações e arquivos planeados, visite a página [O que há de novo no Diretório Ativo do Azure?](../fundamentals/whats-new.md) 

* **maio de 2020 - Capacidade de escrever números de telefone para Workday:** Além do e-mail e nome de utilizador, já pode escrever o número de telefone e o número de telemóvel do Azure AD para o Workday. Para mais detalhes, consulte o tutorial da [aplicação de writeback.](workday-writeback-tutorial.md)

* **abril de 2020 - Suporte para a versão mais recente da Workday Web Services (WWS) API:** Duas vezes por ano em março e setembro, o Workday oferece atualizações ricas em funcionalidades que o ajudam a cumprir os objetivos do seu negócio e a alterar as exigências da força de trabalho. Para acompanhar as novas funcionalidades entregues pelo Workday, pode agora especificar diretamente a versão API da WWS que gostaria de utilizar no URL de ligação. Para obter mais informações sobre como especificar a versão API do Workday, consulte a secção sobre [a configuração da conectividade workday](#part-3-in-the-provisioning-app-configure-connectivity-to-workday-and-active-directory). 

* **jan 2020 - Capacidade de definir atributo de conta ADExpires:** Utilizando a função [NumFromDate,](../app-provisioning/functions-for-customizing-application-data.md#numfromdate) pode agora mapear os campos de data do dia de trabalho, tais como *EndContractDate* ou *StatusTerminationDate*. 

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Para quem é a solução de provisionamento do utilizador mais adequada?

Esta solução de a provisionamento do utilizador workday é ideal para:

* Organizações que desejam uma solução pré-construída e baseada na nuvem para o fornecimento de utilizadores do Workday

* Organizações que exigem o fornecimento direto de utilizadores do Workday para o Ative Directory, ou Azure Ative Directory

* Organizações que exigem que os utilizadores sejam a provisionados utilizando dados obtidos a partir do módulo HCM workday (ver [Get_Workers)](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html)

* Organizações que exigem a adesão, mudança e deixar os utilizadores sincronizados com uma ou mais Florestas de Diretórios Ativos, Domínios e OUs com base apenas em informações de alteração detetadas no módulo HCM workday (ver [Get_Workers)](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html)

* Organizações que usam o Microsoft 365 para e-mail

## <a name="solution-architecture"></a>Arquitetura de Soluções

Esta secção descreve a arquitetura de solução de aprovisionamento de utilizadores de ponta a ponta para ambientes híbridos comuns. Existem dois fluxos relacionados:

* **Fluxo de dados de RH autoritários – do Dia de Trabalho para o Diretório Ativo:** Neste evento de trabalhadores de fluxo (como Novas Contratações, Transferências, Rescisões) ocorrem primeiro na nuvem de inquilino de RH workday e, em seguida, os dados do evento fluem para o Diretório Ativo no local através da Azure AD e do Agente Provisionante. Dependendo do evento, pode levar a operações de criação/atualização/ativação/desativação em AD.
* **Fluxo de writeback – do Diretório Ativo para o Workday:** Uma vez que a criação da conta esteja completa no Ative Directory, é sincronizado com Azure AD através do Azure AD Connect e informações como e-mail, nome de utilizador e número de telefone podem ser escritas de volta para o Workday.

![Descrição geral](./media/workday-inbound-tutorial/wd_overview.png)

### <a name="end-to-end-user-data-flow"></a>Fluxo de dados de utilizador de ponta a ponta

1. A equipa de RH realiza transações de trabalhadores (Joiners/Movers/Leavers ou New Hires/Transfers/Terminations) em Workday HCM
2. O Serviço de Provisionamento AZURE AD executa sincronizações programadas de identidades da Workday HR e identifica alterações que precisam de ser processadas para sincronização com o Ative Directory no local.
3. O Serviço de Provisionamento Azure AD invoca o agente de provisionamento Azure AD Connect Provisioning Com um pedido de carga útil contendo a conta AD criar/atualizar/ativar/desativar as operações.
4. O Agente de Provisionamento AD AD Ad Azure utiliza uma conta de serviço para adicionar/atualizar dados da conta AD.
5. O motor Azure AD Connect / AD Sync funciona com sincronização delta para puxar atualizações em AD.
6. As atualizações do Ative Directory estão sincronizadas com o Azure Ative Directory.
7. Se a aplicação [Workday Writeback](workday-writeback-tutorial.md) estiver configurada, escreve atributos como e-mail, nome de utilizador e número de telefone para Workday.

## <a name="planning-your-deployment"></a>Planejando a sua implantação

Configurar o fornecimento de utilizadores do Workday para o Ative Directory requer um planeamento considerável que abranja diferentes aspetos, tais como:
* Configuração do agente de provisionamento Azure AD Connect 
* Número de aplicações de provisionamento de utilizadores de trabalho para utilizadores AD para implementar
* Selecionando o identificador correspondente certo, atribuindo mapeamento, transformação e filtros de escotagem

Consulte o [plano de implementação de RH em nuvem](../app-provisioning/plan-cloud-hr-provision.md) para obter orientações abrangentes e as melhores práticas recomendadas. 

## <a name="configure-integration-system-user-in-workday"></a>Configure o utilizador do sistema de integração no Workday

Um requisito comum de todos os conectores de fornecimento de dias de trabalho é que eles requerem credenciais de um utilizador do sistema de integração do workday para se conectar à API de Recursos Humanos do Dia de Trabalho. Esta secção descreve como criar um utilizador do sistema de integração no Workday e tem as seguintes secções:

* [Criar um utilizador de sistema de integração](#creating-an-integration-system-user)
* [Criar um grupo de segurança de integração](#creating-an-integration-security-group)
* [Configurar permissões de política de segurança de domínio](#configuring-domain-security-policy-permissions)
* [Configurar permissões de política de segurança de processos empresariais](#configuring-business-process-security-policy-permissions)
* [Ativar alterações na política de segurança](#activating-security-policy-changes)

> [!NOTE]
> É possível contornar este procedimento e, em vez disso, utilizar uma conta de administrador global workday como conta de integração do sistema. Isto pode funcionar bem para as demonstrações, mas não é recomendado para implantações de produção.

### <a name="creating-an-integration-system-user"></a>Criar um utilizador de sistema de integração

**Para criar um utilizador do sistema de integração:**

1. Inscreva-se no seu inquilino workday usando uma conta de administrador. Na **Aplicação Workday**, insira criar o utilizador na caixa de pesquisa e, em seguida, clique em **Criar Utilizador do Sistema de Integração**.

   >[!div class="mx-imgBorder"] 
   >![Criar utilizador](./media/workday-inbound-tutorial/wd_isu_01.png "Criar utilizador")
2. Complete a tarefa **do Utilizador do Sistema de Integração Criar** fornecendo um nome de utilizador e uma palavra-passe para um novo Utilizador do Sistema de Integração.  
  
   * Deixe a **nova palavra-passe requerendo nova palavra-passe na** próxima sessão Na opção não verificada, porque este utilizador irá iniciar sessão programática.
   * Deixe os **Minutos de Tempo de Sessão** com o seu valor predefinido de 0, o que evitará que as sessões do utilizador se acabem prematuramente.
   * Selecione a opção **Não Permitir sessões de UI,** pois fornece uma camada de segurança adicional que impede um utilizador com a palavra-passe do sistema de integração de iniciar sessão no Workday.

   > [!div class="mx-imgBorder"]
   > ![Criar utilizador de sistema de integração](./media/workday-inbound-tutorial/wd_isu_02.png "Criar utilizador de sistema de integração")

### <a name="creating-an-integration-security-group"></a>Criar um grupo de segurança de integração

Neste passo, irá criar um grupo de segurança do sistema de integração descontrangido ou limitado no Workday e atribuir o utilizador do sistema de integração criado no passo anterior a este grupo.

**Para criar um grupo de segurança:**

1. Introduza criar grupo de segurança na caixa de pesquisa e, em seguida, clique em **Criar Grupo de Segurança**.

   > [!div class="mx-imgBorder"]
   > ![Screenshot que mostra "criar grupo de segurança" introduzido na caixa de pesquisa, e "Create Security Group - Task" apresentado nos resultados da pesquisa.](./media/workday-inbound-tutorial/wd_isu_03.png)
2. Complete a tarefa **do Grupo de Segurança Criar.** 

   * Existem dois tipos de grupos de segurança no dia de trabalho:
     * **Sem restrições:** Todos os membros do grupo de segurança podem aceder a todas as instâncias de dados protegidas pelo grupo de segurança.
     * **Limitado:** Todos os membros do grupo de segurança têm acesso contextual a um subconjunto de casos de dados (linhas) a que o grupo de segurança pode aceder.
   * Consulte o seu parceiro de integração workday para selecionar o tipo de grupo de segurança apropriado para a integração.
   * Assim que conhecer o tipo de grupo, selecione **Integration System Security Group (Unconstrained)** ou Integration System Security Group **(Constrained)** do **Tipo de Abandono do Grupo de Segurança Inquilino.**

     > [!div class="mx-imgBorder"]
     >![Criar Grupo de Segurança](./media/workday-inbound-tutorial/wd_isu_04.png "Criar Grupo de Segurança")

3. Após o sucesso da criação do Grupo de Segurança, verá uma página onde poderá atribuir membros ao Grupo de Segurança. Adicione o novo utilizador do sistema de integração criado no passo anterior a este grupo de segurança. Se estiver a utilizar um grupo de segurança *restrito,* também terá de selecionar o âmbito de organização apropriado.

   >[!div class="mx-imgBorder"]
   >![Grupo de Segurança editar](./media/workday-inbound-tutorial/wd_isu_05.png "Grupo de Segurança editar")

### <a name="configuring-domain-security-policy-permissions"></a>Configurar permissões de política de segurança de domínio

Neste passo, você concederá permissões políticas de "segurança de domínio" para os dados dos trabalhadores para o grupo de segurança.

**Para configurar permissões de política de segurança de domínio:**

1. Introduza a **configuração de segurança** do domínio na caixa de pesquisa e, em seguida, clique no relatório de **configuração de segurança do domínio do**link .  
   >[!div class="mx-imgBorder"]
   >![Screenshot que mostra "configuração de segurança de domínio" na caixa de pesquisa, com "Configuração de Segurança do Domínio - Relatório" apresentado nos resultados.](./media/workday-inbound-tutorial/wd_isu_06.png "Políticas de Segurança de Domínio")  
2. Na caixa de texto **do Domínio,** procure os seguintes domínios e adicione-os ao filtro um a um.  
   * *Provisionamento de Conta Externa*
   * *Dados do Trabalhador: Trabalhadores*
   * *Dados dos Trabalhadores: Relatórios dos Trabalhadores Públicos*
   * *Dados de Pessoas: Informações de contacto de trabalho*
   * *Dados do Trabalhador: Todas as posições*
   * *Dados do Trabalhador: Informação atual sobre pessoal*
   * *Dados do Trabalhador: Título do negócio no perfil do trabalhador*
   * *Contas workday*
   
     >[!div class="mx-imgBorder"]
     >![Screenshot que mostra o relatório de configuração de segurança do domínio com a "Conta Externa" na caixa de texto "Domínio".](./media/workday-inbound-tutorial/wd_isu_07.png "Políticas de Segurança de Domínio")  

     >[!div class="mx-imgBorder"]
     >![Screenshot que mostra o relatório de configuração de segurança do domínio com uma lista de domínios selecionados.](./media/workday-inbound-tutorial/wd_isu_08.png "Políticas de Segurança de Domínio") 

     Clique em **OK**.

3. No relatório que aparece, selecione a elipse (...) que aparece ao lado **do Provisionamento de Conta Externa** e clique na opção menu Domínio **-> Editar Permissões de Política de Segurança**
   >[!div class="mx-imgBorder"]
   >![Políticas de Segurança de Domínio](./media/workday-inbound-tutorial/wd_isu_09.png "Políticas de Segurança de Domínio")  

4. Na página **de Permissões de Política de Segurança do Domínio de Edição,** desloque-se até à secção **Permissões de Integração**. Clique no sinal "+" para adicionar o grupo de sistema de integração à lista de grupos de segurança com permissões de integração **Get** e **Put.**
   >[!div class="mx-imgBorder"]
   >![Screenshot que mostra a secção "Permissões de Integração" realçada.](./media/workday-inbound-tutorial/wd_isu_10.png "Editar Permissão")  

5. Clique no sinal "+" para adicionar o grupo de sistema de integração à lista de grupos de segurança com permissões de integração **Get** e **Put.**

   >[!div class="mx-imgBorder"]
   >![Editar Permissão](./media/workday-inbound-tutorial/wd_isu_11.png "Editar Permissão")  

6. Repita os passos 3-5 acima para cada uma destas políticas de segurança restantes:

   | Operação | Política de Segurança de Domínio |
   | ---------- | ---------- |
   | Obter e Colocar | Dados dos Trabalhadores: Relatórios dos Trabalhadores Públicos |
   | Obter e Colocar | Dados de Pessoas: Informações de contacto de trabalho |
   | Get | Dados do Trabalhador: Trabalhadores |
   | Get | Dados do Trabalhador: Todas as posições |
   | Get | Dados do Trabalhador: Informação atual sobre pessoal |
   | Get | Dados do Trabalhador: Título do negócio no perfil do trabalhador |
   | Obter e Colocar | Contas workday |

### <a name="configuring-business-process-security-policy-permissions"></a>Configurar permissões de política de segurança de processos empresariais

Neste passo, você concederá permissões políticas de "segurança do processo de negócio" para os dados dos trabalhadores para o grupo de segurança. 

> [!NOTE]
> Este passo é necessário apenas para configurar o conector de aplicação Workday Writeback.

**Para configurar permissões de política de segurança do processo de negócio:**

1. Introduza **a Política de Processos Empresariais** na caixa de pesquisa e, em seguida, clique na tarefa **de Política de Segurança do Processo de Negócio de Edição** de Link.  

   >[!div class="mx-imgBorder"]
   >![Screenshot que mostra "Política de Processo de Negócio" na caixa de pesquisa e "Editar Política de Segurança do Processo De Negócio - Tarefa" selecionado.](./media/workday-inbound-tutorial/wd_isu_12.png "Políticas de Segurança do Processo de Negócios")  

2. Na caixa de texto **do Tipo de Processo De Negócio,** procure *contacto* e selecione processo de negócio de Mudança de Contacto **de Trabalho** e clique em **OK**.

   >[!div class="mx-imgBorder"]
   >![Screenshot que mostra a página "Editar Política de Segurança do Processo de Negócio" e "Mudança de Contacto de Trabalho" selecionada no menu "Business Process Type".](./media/workday-inbound-tutorial/wd_isu_13.png "Políticas de Segurança do Processo de Negócios")  

3. Na página **Política de Segurança do Processo de Edição,** percorra para a secção Change Work Contact Information **(Serviço Web).**
    

4. Selecione e adicione o novo grupo de segurança do sistema de integração à lista de grupos de segurança que podem iniciar o pedido de serviços web. 

   >[!div class="mx-imgBorder"]
   >![Políticas de Segurança do Processo de Negócios](./media/workday-inbound-tutorial/wd_isu_15.png "Políticas de Segurança do Processo de Negócios")  

5. Clique em **'Feito'.** 

### <a name="activating-security-policy-changes"></a>Ativar alterações na política de segurança

**Para ativar as mudanças na política de segurança:**

1. Introduza ativar-se na caixa de pesquisa e, em seguida, clique no link **Ativar Alterações da Política de Segurança Pendentes**.
   >[!div class="mx-imgBorder"]
   >![Ativar](./media/workday-inbound-tutorial/wd_isu_16.png "Ativar")

1. Inicie a tarefa De alterar a política de segurança pendente, introduzindo um comentário para efeitos de auditoria e, em seguida, clique em **OK**.
1. Complete a tarefa no ecrã seguinte verificando a caixa de verificação **Confirmar**e, em seguida, clique em **OK**.

   >[!div class="mx-imgBorder"]
   >![Ativar segurança pendente](./media/workday-inbound-tutorial/wd_isu_18.png "Ativar segurança pendente")  

## <a name="configure-active-directory-service-account"></a>Configure conta de serviço de diretório ativo

Esta secção descreve as permissões da conta de serviço AD necessárias para instalar e configurar o Agente de Provisionamento AD AD Azure.

### <a name="permissions-required-to-run-the-provisioning-agent-installer"></a>Permissões necessárias para executar o Instalador de Agente de Provisioning
Depois de ter identificado o Servidor do Windows que irá hospedar o Agente de Provisionamento, inicie sessão no anfitrião do servidor utilizando credenciais de administração local ou de administração de domínio. O processo de configuração do agente cria ficheiros credenciais de loja de chaves seguros e atualiza a configuração do perfil de serviço no servidor anfitrião. Isto requer acesso administrativo no servidor que hospeda o agente. 

### <a name="permissions-required-to-configure-the-provisioning-agent-service"></a>Permissões necessárias para configurar o serviço de Agente de Provisioning
Utilize os passos abaixo para configurar uma conta de serviço que pode ser usada para operações de provisionamento de agentes. 
1.  No seu Controlador de Domínio AD, abra *os utilizadores de diretório ativo e o* snap-in de computadores. 
2.  Criar um novo utilizador de domínio (exemplo: *provAgentAdmin)*  
3.  Clique no nome de ou de domínio ou ou do domínio e selecione O Controlo de *Delegados* que abrirá a *Delegação do Assistente de Controlo*. 

> [!NOTE] 
> Se pretender limitar o agente de provisionamento apenas para criar e ler os utilizadores de um determinado OU para efeitos de teste, recomendamos a delegação do controlo ao nível adequado da U durante os ensaios.

4. Clique em **seguida** no ecrã de boas-vindas. 
5. No ecrã **Select Users ou Groups,** adicione o utilizador de domínio criado no passo 2. Clique em **Seguinte**.
   >[!div class="mx-imgBorder"]
   >![Adicionar tela](./media/workday-inbound-tutorial/delegation-wizard-01.png "Adicionar tela")

6. No ecrã **Tasks to Delegado,** selecione as seguintes tarefas: 
   * Criar, eliminar e gerir contas de utilizador
   * Leia todas as informações do utilizador

   >[!div class="mx-imgBorder"]
   >![Tela de tarefas](./media/workday-inbound-tutorial/delegation-wizard-02.png "Tela de tarefas")

7. Clique **em Seguinte** e **Guarde** a configuração


## <a name="configuring-user-provisioning-from-workday-to-active-directory"></a>Configurar o fornecimento de utilizadores de Workday a Ative Directory

Esta secção fornece passos para o fornecimento de conta de utilizador de Workday a cada domínio ative directory no âmbito da sua integração.

* [Adicione a app de conector de provisionamento e descarregue o Agente De provisionador](#part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent)
* [Instalação e configuração no local Agente de provisionamento](#part-2-install-and-configure-on-premises-provisioning-agents)
* [Configure a conectividade ao Workday e ao Ative Directory](#part-3-in-the-provisioning-app-configure-connectivity-to-workday-and-active-directory)
* [Configurar mapeamentos de atributos](#part-4-configure-attribute-mappings)
* [Permitir e lançar o fornecimento de utilizadores](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent"></a>Parte 1: Adicione a app de conector de provisionamento e descarregue o Agente De provisionador

**Para configurar o Workday para o provisionamento do Ative Directory:**

1. Aceda a <https://portal.azure.com>.

2. No portal Azure, procure e selecione **O Diretório Ativo Azure**.

3. Selecione **Aplicações empresariais,** em seguida, **todas as aplicações**.

4. **Selecione Adicione uma aplicação**e selecione a categoria **'Todos'.**

5. Procure por **Workday para Ative Directory User Provisioning**, e adicione essa aplicação na galeria.

6. Depois de adicionar a aplicação e mostrar o ecrã de detalhes da aplicação, selecione **Provisioning**.

7. Altere o **modo** **de provisionamento** para **Automático**.

8. Clique no banner de informações exibido para descarregar o Agente Provisioning. 

   >[!div class="mx-imgBorder"]
   >![Agente de descarregamento](./media/workday-inbound-tutorial/pa-download-agent.png "Download Agent Screen")


### <a name="part-2-install-and-configure-on-premises-provisioning-agents"></a>Parte 2: Instalar e configurar no local agentes de provisionamento

Para o fornecimento ao Ative Directory no local, o agente Provisioning deve ser instalado num servidor que tenha .NET 4.7.1+ Framework e acesso à rede ao domínio ou do Diretório Ativo pretendido.

> [!TIP]
> Pode verificar a versão da estrutura .NET no seu servidor utilizando as instruções fornecidas [aqui.](/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed)
> Se o servidor não tiver .NET 4.7.1 ou superior instalado, pode descarregá-lo a partir [daqui](https://support.microsoft.com/help/4033342/the-net-framework-4-7-1-offline-installer-for-windows).  

Transfira o instalador de agente descarregado para o anfitrião do servidor e siga os passos abaixo para completar a configuração do agente.

1. Faça o sposição no Windows Server onde pretende instalar o novo agente.

1. Lance o instalador do Agente de Provisionamento, concorde com os termos e clique no botão **Instalar.**

   >[!div class="mx-imgBorder"]
   >![Instalar tela](./media/workday-inbound-tutorial/pa_install_screen_1.png "Instalar tela")
   
1. Após a instalação estar concluída, o assistente será lançado e verá o ecrã **AD Connect Azure.** Clique no botão **Authenticate** para ligar à sua instância AD Azure.

   >[!div class="mx-imgBorder"]
   >![Ligar ao Azure AD](./media/workday-inbound-tutorial/pa_install_screen_2.png "Ligar ao Azure AD")
   
1. Autenticar para a sua instância AD Azure usando credenciais de administrador de identidade híbrida.

   >[!div class="mx-imgBorder"]
   >![Admin Auth](./media/workday-inbound-tutorial/pa_install_screen_3.png "Admin Auth")

   > [!NOTE]
   > As credenciais de administração da AD Azure são usadas apenas para ligar ao seu inquilino AZure AD. O agente não armazena as credenciais localmente no servidor.

1. Após a autenticação bem sucedida com a Azure AD, verá o ecrã **do Diretório Ativo Connect.** Neste passo, insira o nome de domínio AD e clique no botão **Adicionar Diretório.**

   >[!div class="mx-imgBorder"]
   >![Adicionar Diretório](./media/workday-inbound-tutorial/pa_install_screen_4.png "Adicionar Diretório")
  
1. Será agora solicitado que introduza as credenciais necessárias para se ligar ao Domínio AD. No mesmo ecrã, pode utilizar a prioridade do **controlador de domínio Select** para especificar os controladores de domínio que o agente deve utilizar para enviar pedidos de provisionamento.

   >[!div class="mx-imgBorder"]
   >![Credenciais de Domínio](./media/workday-inbound-tutorial/pa_install_screen_5.png)
   
1. Depois de configurar o domínio, o instalador apresenta uma lista de domínios configurados. Neste ecrã, pode repetir #5 e #6 para adicionar mais domínios ou clicar em **Next** para proceder ao registo de agente.

   >[!div class="mx-imgBorder"]
   >![Domínios configurados](./media/workday-inbound-tutorial/pa_install_screen_6.png "Domínios configurados")

   > [!NOTE]
   > Se tiver vários domínios de AD (por exemplo, na.contoso.com, emea.contoso.com), adicione cada domínio individualmente à lista.
   > Apenas a adição do domínio dos pais (por exemplo, contoso.com) não é suficiente. Deve registar cada domínio infantil com o agente.
   
1. Reveja os detalhes de configuração e clique em **Confirmar** para registar o agente.
  
   >[!div class="mx-imgBorder"]
   >![Confirmar Tela](./media/workday-inbound-tutorial/pa_install_screen_7.png "Confirmar Tela")
   
1. O assistente de configuração apresenta o progresso do registo do agente.
  
   >[!div class="mx-imgBorder"]
   >![Registo de Agentes](./media/workday-inbound-tutorial/pa_install_screen_8.png "Registo de Agentes")
   
1. Assim que o registo do agente for bem sucedido, pode clicar na **Saída** para sair do Assistente.

   >[!div class="mx-imgBorder"]
   >![Tela de saída](./media/workday-inbound-tutorial/pa_install_screen_9.png "Tela de saída")
   
1. Verifique a instalação do Agente e certifique-se de que está em execução abrindo o Snap-In "Serviços" e procure o Serviço denominado "Microsoft Azure AD Connect Provisioning Agent"

   >[!div class="mx-imgBorder"]
   >![Screenshot do Agente de Provisionamento de Ligação AD da Microsoft Azure em execução em Serviços.](./media/workday-inbound-tutorial/services.png)

### <a name="part-3-in-the-provisioning-app-configure-connectivity-to-workday-and-active-directory"></a>Parte 3: Na aplicação de provisionamento, configurar a conectividade ao Workday e ao Ative Directory
Neste passo, estabelecemos conectividade com o Workday e o Ative Directory no portal Azure. 

1. No portal Azure, volte ao Workday para a App de Provisionamento de Utilizadores do Diretório Ativo criada na [Parte 1](#part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent)

1. Preencha a secção **credenciais de administração** da seguinte forma:

   * **Nome de utilizador do dia útil** – Introduza o nome de utilizador da conta do sistema de integração workday, com o nome de domínio do inquilino anexado. Deve parecer algo como: **nome de utilizador \@ tenant_name**

   * **Senha do dia de trabalho -** Introduza a palavra-passe da conta do sistema de integração workday

   * **URL API de Serviços Web workday –** Introduza o URL no ponto final dos serviços web workday para o seu inquilino. O URL determina a versão da API dos Serviços Web workday utilizada pelo conector. 
   
     | Formato do URL | Versão API da WWS usada | Alterações XPATH necessárias |
     |------------|----------------------|------------------------|
     | https://####.workday.com/ccx/service/tenantName | v21.1 | No |
     | https://####.workday.com/ccx/service/tenantName/Human_Resources | v21.1 | No |
     | https://####.workday.com/ccx/service/tenantName/Human_Resources/v##.# | v## . # | Yes |

      > [!NOTE]
     > Se nenhuma informação de versão for especificada no URL, a aplicação utiliza o Workday Web Services (WWS) v21.1 e não são necessárias alterações nas expressões API padrão enviadas com a aplicação. Para utilizar uma versão API da WWS específica, especifique o número da versão no URL <br>
     > Exemplo: `https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources/v34.0` <br>
     > <br> Se estiver a utilizar uma lista de atributos de Edição de API wws v30.0+, antes de ligar o trabalho de provisionamento, por favor atualize as **expressões API XPATH** em **"O Mapeamento de Atributos > Opções Avançadas -> Editar lista de atributos para Workday** referente à secção Gerir a [sua configuração](#managing-your-configuration) e [referência de atributos workday](../app-provisioning/workday-attribute-reference.md#xpath-values-for-workday-web-services-wws-api-v30).  

   * **Floresta de Diretório Ativo -** O "Nome" do seu domínio ative directory, registado no agente. Utilize o dropdown para selecionar o domínio alvo para o provisionamento. Este valor é tipicamente uma cadeia como: *contoso.com*

   * **Contentor de Diretório Ativo -** Introduza o contentor DN onde o agente deve criar contas de utilizador por padrão.
        Exemplo: *OU=Utilizadores Padrão,OU=Utilizadores,DC=contoso,DC=teste*
        
     > [!NOTE]
     > Esta definição só entra em jogo para criações de conta de utilizador se o atributo *nome parental* não estiver configurado nos mapeamentos do atributo. Esta definição não é utilizada para operações de pesquisa ou atualização do utilizador. Toda a sub-árvore de domínio cai no âmbito da operação de busca.

   * **E-mail de notificação –** Insira o seu endereço de e-mail e verifique a caixa de verificação "enviar e-mail se ocorrer falha".

     > [!NOTE]
     > O Serviço de Provisionamento Azure AD envia uma notificação por e-mail se o trabalho de provisionamento entrar em estado de [quarentena.](../app-provisioning/application-provisioning-quarantine-status.md)

   * Clique no botão **De Ligação de Teste.** Se o teste de ligação for bem sucedido, clique no botão **Guardar** na parte superior. Se falhar, verifique duas vezes se as credenciais do Workday e as credenciais AD configuradas na configuração do agente são válidas.

     >[!div class="mx-imgBorder"]
     >![Screenshot que mostra a página "Provisioning" com credenciais inseridas.](./media/workday-inbound-tutorial/wd_1.png)

   * Uma vez guardadas as credenciais com sucesso, a secção **de Mapeamentos** apresentará o mapeamento padrão **sincronizar trabalhadores do workday para on premis Ative Directory**

### <a name="part-4-configure-attribute-mappings"></a>Parte 4: Configurar mapeamentos de atributos

Nesta secção, irá configurar como os dados dos utilizadores fluem do Workday para o Ative Directory.

1. No separador De Provisionamento em **Mappings,** clique em **Sincronizar Trabalhadores do Workday para On Premises Ative Directory**.

1. No campo **'Âmbito do Objeto fonte',** pode selecionar quais os conjuntos de utilizadores no Workday que devem estar em possibilidade de provisão para AD, definindo um conjunto de filtros baseados em atributos. O âmbito padrão é "todos os utilizadores no Workday". Filtros de exemplo:

   * Exemplo: Âmbito para utilizadores com IDs de trabalhador entre 1000000 e 2000000 (excluindo 2000000)

      * Atributo: WorkerID

      * Operador: REGEX Match

      * Valor: (1[0-9][0-9][0-9][0-9][0-9][0-9][0-9])

   * Exemplo: Apenas trabalhadores e não trabalhadores contingentes

      * Atributo: EmployeeID

      * Operador: NÃO É NULO

   > [!TIP]
   > Quando estiver a configurar a app de provisionamento pela primeira vez, terá de testar e verificar os mapeamentos e expressões do seu atributo para se certificar de que está a dar-lhe o resultado desejado. A Microsoft recomenda a utilização dos filtros de deteção no **âmbito do objeto de origem** para testar os seus mapeamentos com alguns utilizadores de teste do Workday. Depois de verificar que os mapeamentos funcionam, pode remover o filtro ou expandi-lo gradualmente para incluir mais utilizadores.

   > [!CAUTION] 
   > O comportamento predefinido do motor de provisionamento é desativar/eliminar utilizadores que ficam fora de alcance. Isto pode não ser desejável no seu dia de trabalho para a integração da AD. Para anular este comportamento padrão consulte o artigo [Ignorar a eliminação das contas de utilizador que ficam fora de alcance](../app-provisioning/skip-out-of-scope-deletions.md)
  
1. No campo **Target Object Actions,** pode filtrar globalmente as ações que são realizadas no Ative Directory. **Criar** e **Atualizar** são mais comuns.

1. Na secção **de mapeamentos do Atributo,** pode definir como o mapa individual do Dia de Trabalho atribui o mapa aos atributos do Ative Directory.

1. Clique num mapeamento de atributos existente para atualizá-lo ou clique em **Adicionar novo mapeamento** na parte inferior do ecrã para adicionar novos mapeamentos. Um mapeamento de atributos individuais suporta estas propriedades:

      * **Tipo de Mapeamento**

         * **Direto** – Escreve o valor do atributo Workday ao atributo AD, sem alterações

         * **Constante** - Escreva um valor estático e constante de cordas para o atributo AD

         * **Expressão** – Permite-lhe escrever um valor personalizado ao atributo AD, com base num ou mais atributos do Dia do Trabalho. [Para mais informações, consulte este artigo sobre expressões.](../app-provisioning/functions-for-customizing-application-data.md)

      * **Atributo de origem** - O atributo do utilizador do Workday. Se o atributo que procura não estiver presente, consulte [personalizar a lista de atributos do utilizador do Workday](#customizing-the-list-of-workday-user-attributes).

      * **Valor predefinido** – Opcional. Se o atributo de origem tiver um valor vazio, o mapeamento escreverá este valor.
            A configuração mais comum é deixar este em branco.

      * **Atributo-alvo** - O atributo do utilizador no Ative Directory.

      * **Combine objetos que utilizem este atributo** – Se este mapeamento deve ou não ser usado para identificar exclusivamente os utilizadores entre o Workday e o Ative Directory. Este valor é tipicamente definido no campo de ID do Trabalhador para o Dia de Trabalho, que é tipicamente mapeado para um dos atributos de ID do Empregado no Ative Directory.

      * **Precedência correspondente** - Podem ser definidos vários atributos correspondentes. Quando há múltiplos, são avaliados na ordem definida por este campo. Assim que um fósforo é encontrado, não são avaliados mais atributos correspondentes.

      * **Aplique este mapeamento**

         * **Always** – Aplicar este mapeamento tanto na criação como nas ações de atualização do utilizador

         * **Apenas durante a criação** - Aplique este mapeamento apenas em ações de criação de utilizadores

1. Para guardar os seus mapeamentos, clique em **Guardar** na parte superior da secção Attribute-Mapping.
   >[!div class="mx-imgBorder"]
   >![Screenshot que mostra a página "Atribuir Mapeamento" com a ação "Save" selecionada.](./media/workday-inbound-tutorial/wd_2.png)

#### <a name="below-are-some-example-attribute-mappings-between-workday-and-active-directory-with-some-common-expressions"></a>Abaixo estão alguns mapeamentos de atributos de exemplo entre Workday e Ative Directory, com algumas expressões comuns

* A expressão que mapeia para o atributo *nome de paiDistinguished* é usada para providenciar um utilizador a diferentes OUs com base em um ou mais atributos de origem workday. Este exemplo aqui coloca os utilizadores em diferentes OUs com base em que cidade estão.

* O *atributo userPrincipalName* no Ative Directory é gerado utilizando a função de des duplicação [SelectUniqueValue](../app-provisioning/functions-for-customizing-application-data.md#selectuniquevalue) que verifica a existência de um valor gerado no domínio de AD alvo e só o define se for único.  

* [Há documentação sobre a escrita de expressões aqui.](../app-provisioning/functions-for-customizing-application-data.md) Esta secção inclui exemplos sobre como remover caracteres especiais.

| ATRIBUTO WORKDAY | ATRIBUTO DE DIRETÓRIO ATIVO |  CORRESPONDÊNCIA DE ID? | CRIAR / ATUALIZAR |
| ---------- | ---------- | ---------- | ---------- |
| **TrabalhadorID**  |  EmployeeID | **Sim** | Escrito apenas na criação |
| **Natalmelível FavoritoData**    |  cn    |   |   Escrito apenas na criação |
| **SelectUniqueValue( \@ Join(" Join(".",  \[ FirstName \] , \[ LastName \] , "contoso.com"), \@ Join(" Join(".", Mid( \[ FirstName, \] 1, \[ LastName), \] "contoso.com"), Join(" \@ Join(".", Mid( \[ FirstName, \] 1, 2), \[ \] LastName), "contoso.com"))**   | userPrincipalName     |     | Escrito apenas na criação 
| `Replace(Mid(Replace(\[UserID\], , "(\[\\\\/\\\\\\\\\\\\\[\\\\\]\\\\:\\\\;\\\\\|\\\\=\\\\,\\\\+\\\\\*\\\\?\\\\&lt;\\\\&gt;\])", , "", , ), 1, 20), , "([\\\\.)\*\$](file:///\\.)*$)", , "", , )`      |    sAMAccountName            |     |         Escrito apenas na criação |
| **Switch( \[ Ative , " \] 0", "True", "1", "False")** |  contadisável      |     | Criar + atualização |
| **FirstName**   | nomeDado       |     |    Criar + atualização |
| **LastName**   |   sn   |     |  Criar + atualização |
| **Natalmelível FavoritoData**  |  displayName |     |   Criar + atualização |
| **Empresa**         | empresa   |     |  Criar + atualização |
| **SupervisãoOrganização**  | departamento  |     |  Criar + atualização |
| **Referência do Gestor**   | gestor  |     |  Criar + atualização |
| **BusinessTitle**   |  título     |     |  Criar + atualização | 
| **EndereçoLineData**    |  streetAddress  |     |   Criar + atualização |
| **Município**   |   l   |     | Criar + atualização |
| **CountryReferenceTwoLetter**      |   co |     |   Criar + atualização |
| **CountryReferenceTwoLetter**    |  c  |     |         Criar + atualização |
| **Conferência CountryRegionReference** |  SC     |     | Criar + atualização |
| **WorkSpaceReference** | físicoDeliveryOfficeName    |     |  Criar + atualização |
| **PostalCode**  |   código postal  |     | Criar + atualização |
| **Telefone primaryWorkTelephone**  |  número de telefone   |     | Criar + atualização |
| **Fax**      | facsimileTelephoneNumber     |     |    Criar + atualização |
| **Móvel**  |    dispositivo móvel       |     |       Criar + atualização |
| **Referência local** |  preferiuLanguage  |     |  Criar + atualização |                                               
| **Switch( \[ Município \] , "OU=Utilizadores Predefinidos,DC=contoso,DC=com", "Dallas", "OU=Dallas,OU=Users,DC=contoso,DC=com", "Austin", "OU=Austin,OU=Users,DC=contoso,DC=com", "Seattle", "Ou=Seattle,OU=Users,DC=contoso,DC=com", "London", "OU=London,OU=Utilizadores,DC=contoso",d.C.)**  | parentAdome     |     |  Criar + atualização |

Uma vez concluída a configuração de mapeamento do seu atributo, pode agora [ativar e lançar o serviço de fornecimento de utilizadores](#enable-and-launch-user-provisioning).

## <a name="enable-and-launch-user-provisioning"></a>Permitir e lançar o fornecimento de utilizadores

Uma vez concluídas as configurações da aplicação de provisionamento workday, pode ligar o serviço de prestação no portal Azure.

> [!TIP]
> Por padrão, quando ligar o serviço de fornecimento, iniciará operações de provisionamento para todos os utilizadores no âmbito. Se houver erros no mapeamento ou problemas de dados do Dia de Trabalho, então o trabalho de provisionamento pode falhar e entrar no estado de quarentena. Para evitar isto, como uma boa prática, recomendamos configurar o filtro **Source Object Scope** e testar os mapeamentos do seu atributo com alguns utilizadores de teste antes de lançar a sincronização completa para todos os utilizadores. Uma vez verificado que os mapeamentos funcionam e lhe estão a dar os resultados desejados, então pode remover o filtro ou expandi-lo gradualmente para incluir mais utilizadores.

1. No **separador Provisioning,** desa fixação do **Estado de Provisionamento** para **On**.

2. Clique em **Guardar**.

3. Esta operação iniciará a sincronização inicial, que pode demorar um número variável de horas dependendo de quantos utilizadores estão no arrendatário do Workday. 

4. A qualquer momento, consulte o **separador de registos** de auditoria no portal Azure para ver que ações o serviço de prestação de serviços executou. Os registos de auditoria listam todos os eventos de sincronização individuais realizados pelo serviço de fornecimento, tais como os utilizadores que estão a ser lidos fora do Workday e posteriormente adicionados ou atualizados ao Ative Directory. Consulte a secção de resolução de problemas para obter instruções sobre como rever os registos de auditoria e corrigir erros de provisionamento.

5. Uma vez concluída a sincronização inicial, escreverá um relatório de resumo de auditoria no **separador Provisioning,** como mostrado abaixo.
   > [!div class="mx-imgBorder"]
   > ![Provisão de barras de progresso](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)



## <a name="frequently-asked-questions-faq"></a>Perguntas Frequentes (FAQ)

* **Questões de capacidade de solução**
  * [Ao processar um novo aluguer do Workday, como é que a solução define a palavra-passe para a nova conta de utilizador no Ative Directory?](#when-processing-a-new-hire-from-workday-how-does-the-solution-set-the-password-for-the-new-user-account-in-active-directory)
  * [A solução suporta o envio de notificações por e-mail após o fornecimento de operações concluídas?](#does-the-solution-support-sending-email-notifications-after-provisioning-operations-complete)
  * [Como posso gerir a entrega de senhas para novas contratações e fornecer de forma segura um mecanismo para redefinir a sua palavra-passe?](#how-do-i-manage-delivery-of-passwords-for-new-hires-and-securely-provide-a-mechanism-to-reset-their-password)
  * [A solução cache Perfis de utilizador do Workday na nuvem AZure AD ou na camada de agente de provisionamento?](#does-the-solution-cache-workday-user-profiles-in-the-azure-ad-cloud-or-at-the-provisioning-agent-layer)
  * [O suporte à solução que atribui grupos de AD no local ao utilizador?](#does-the-solution-support-assigning-on-premises-ad-groups-to-the-user)
  * [Que APIs workday utiliza a solução para consultar e atualizar os perfis dos trabalhadores do Workday?](#which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles)
  * [Posso configurar o meu inquilino do Workday HCM com dois inquilinos da AD Azure?](#can-i-configure-my-workday-hcm-tenant-with-two-azure-ad-tenants)
  * [Por que razão a aplicação de provisionamento do utilizador "Workday to Azure AD" não é suportada se implementamos o Azure AD Connect?](#why-workday-to-azure-ad-user-provisioning-app-is-not-supported-if-we-have-deployed-azure-ad-connect)
  * [Como posso sugerir melhorias ou solicitar novas funcionalidades relacionadas com a integração do Workday e da AD Azure?](#how-do-i-suggest-improvements-or-request-new-features-related-to-workday-and-azure-ad-integration)

* **Questões do Agente de Provisionamento**
  * [Qual é a versão GA do Agente Provisionante?](#what-is-the-ga-version-of-the-provisioning-agent)
  * [Como conheço a versão do meu agente de provisionamento?](#how-do-i-know-the-version-of-my-provisioning-agent)
  * [A Microsoft introduz automaticamente atualizações do Agente de Provisioning?](#does-microsoft-automatically-push-provisioning-agent-updates)
  * [Posso instalar o Agente de Provisioning no mesmo servidor que executa o Azure AD Connect?](#can-i-install-the-provisioning-agent-on-the-same-server-running-azure-ad-connect)
  * [Como posso configurar o Agente Provisioning para utilizar um servidor proxy para comunicação HTTP de saída?](#how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication)
  * [Como posso assegurar que o Agente de Provisionamento seja capaz de comunicar com o inquilino Azure AD e que não haja firewalls a bloquear portas exigidas pelo agente?](#how-do-i-ensure-that-the-provisioning-agent-is-able-to-communicate-with-the-azure-ad-tenant-and-no-firewalls-are-blocking-ports-required-by-the-agent)
  * [Como posso desscamur o domínio associado ao meu Agente de Provisionamento?](#how-do-i-de-register-the-domain-associated-with-my-provisioning-agent)
  * [Como desinstalar o Agente de Provisionamento?](#how-do-i-uninstall-the-provisioning-agent)
  
* **Perguntas de mapeamento e configuração de atributos do dia de trabalho para AD**
  * [Como posso fazer o back up ou exportar uma cópia de trabalho do meu Trabalhoday Provisioning Attribute Mapping e Schema?](#how-do-i-back-up-or-export-a-working-copy-of-my-workday-provisioning-attribute-mapping-and-schema)
  * [Tenho atributos personalizados no Workday e no Ative Directory. Como posso configurar a solução para trabalhar com os meus atributos personalizados?](#i-have-custom-attributes-in-workday-and-active-directory-how-do-i-configure-the-solution-to-work-with-my-custom-attributes)
  * [Posso providenciar a foto do utilizador do Workday ao Ative Directory?](#can-i-provision-users-photo-from-workday-to-active-directory)
  * [Como posso sincronizar os números de telemóvel do Workday com base no consentimento do utilizador para uso público?](#how-do-i-sync-mobile-numbers-from-workday-based-on-user-consent-for-public-usage)
  * [Como posso formatr os nomes em AD com base nos atributos do departamento/país/cidade do utilizador e lidar com as variações regionais?](#how-do-i-format-display-names-in-ad-based-on-the-users-departmentcountrycity-attributes-and-handle-regional-variances)
  * [Como posso utilizar o SelectUniqueValue para gerar valores únicos para o atributo samAccountName?](#how-can-i-use-selectuniquevalue-to-generate-unique-values-for-samaccountname-attribute)
  * [Como removo caracteres com diacríticos e converto-os em alfabetos ingleses normais?](#how-do-i-remove-characters-with-diacritics-and-convert-them-into-normal-english-alphabets)

### <a name="solution-capability-questions"></a>Questões de capacidade de solução

#### <a name="when-processing-a-new-hire-from-workday-how-does-the-solution-set-the-password-for-the-new-user-account-in-active-directory"></a>Ao processar um novo aluguer do Workday, como é que a solução define a palavra-passe para a nova conta de utilizador no Ative Directory?

Quando o agente de provisionamento no local recebe um pedido de criação de uma nova conta AD, gera automaticamente uma senha aleatória complexa projetada para satisfazer os requisitos de complexidade da palavra-passe definidos pelo servidor AD e define-a no objeto do utilizador. Esta palavra-passe não está registada em lado nenhum.

#### <a name="does-the-solution-support-sending-email-notifications-after-provisioning-operations-complete"></a>A solução suporta o envio de notificações por e-mail após o fornecimento de operações concluídas?

Não, o envio de notificações por e-mail após o preenchimento das operações de provisionamento não é suportado na versão atual.

#### <a name="how-do-i-manage-delivery-of-passwords-for-new-hires-and-securely-provide-a-mechanism-to-reset-their-password"></a>Como posso gerir a entrega de senhas para novas contratações e fornecer de forma segura um mecanismo para redefinir a sua palavra-passe?

Um dos passos finais envolvidos no novo provisionamento de conta AD é a entrega da senha temporária atribuída à conta AD do utilizador. Muitas empresas ainda usam a abordagem tradicional de entregar a senha temporária ao gestor do utilizador, que depois entrega a senha ao novo contratado/trabalhador contingente. Este processo tem uma falha de segurança inerente e existe uma opção disponível para implementar uma melhor abordagem usando as capacidades AD do Azure.

Como parte do processo de contratação, as equipas de RH geralmente fazem uma verificação de antecedentes e verificam o número de telemóvel do novo aluguer. Com a integração do Workday to AD User Provisioning, pode construir além deste facto e lançar uma capacidade de reset de senha de autosserviço para o utilizador no dia 1. Isto é conseguido através da propagação do atributo "Número Móvel" do novo contrato de Workday para AD e, em seguida, de AD a AD AD usando Azure AD Connect. Uma vez presente o "Número Móvel" no Azure AD, pode ativar o Reset de [Palavra-Passe de Autosserviço (SSPR)](../authentication/howto-sspr-authenticationdata.md) para a conta do utilizador, para que no dia 1, um novo aluguer possa utilizar o número de telemóvel registado e verificado para autenticação.

#### <a name="does-the-solution-cache-workday-user-profiles-in-the-azure-ad-cloud-or-at-the-provisioning-agent-layer"></a>A solução cache Perfis de utilizador do Workday na nuvem AZure AD ou na camada de agente de provisionamento?

Não, a solução não mantém uma cache de perfis de utilizador. O serviço de fornecimento de Azure AD simplesmente funciona como um processador de dados, lendo dados do Workday e escrevendo para o diretório ativo alvo ou Azure AD. Consulte a secção [Gerir dados pessoais](#managing-personal-data) para obter detalhes relacionados com a privacidade do utilizador e a retenção de dados.

#### <a name="does-the-solution-support-assigning-on-premises-ad-groups-to-the-user"></a>O suporte à solução que atribui grupos de AD no local ao utilizador?

Esta funcionalidade não é suportada atualmente. A solução recomendada é implementar um script PowerShell que questione o ponto final da Microsoft Graph API para [os dados de registo de auditoria](/graph/api/resources/azure-ad-auditlog-overview?view=graph-rest-beta) e use-o para desencadear cenários como a atribuição de grupos. Este script PowerShell pode ser anexado a um programador de tarefas e implantado na mesma caixa que executa o agente de provisionamento.  

#### <a name="which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles"></a>Que APIs workday utiliza a solução para consultar e atualizar os perfis dos trabalhadores do Workday?

A solução utiliza atualmente as seguintes APIs do dia de trabalho:

* O formato **URL API dos Serviços Web workday** utilizado na secção **Credenciais de Administração,** determina a versão API utilizada para Get_Workers
  * Se o formato URL for: https:// \# \# \# \# \. dia de trabalho \. com/ccx/service/tenantName , então a API v21.1 é utilizada. 
  * Se o formato URL for: https:// \# \# \# \# \. dia de trabalho \. com/ccx/service/tenantName/Recursos \_ Humanos, então a API v21.1 é utilizada 
  * Se o formato URL for: https:// \# \# \# \# \. dia de trabalho \. com/ccx/service/tenantName/Recursos \_ Humanos/v, \# \# \. \# então a versão API especificada é utilizada. (Exemplo: se o v34.0 for especificado, então é usado.)  
   
* Funcionalidade de writeback de email do dia de trabalho utiliza Change_Work_Contact_Information (v30.0) 
* Função de writeback do nome de utilizador do dia útil utiliza Update_Workday_Account (v31.2) 

#### <a name="can-i-configure-my-workday-hcm-tenant-with-two-azure-ad-tenants"></a>Posso configurar o meu inquilino do Workday HCM com dois inquilinos da AD Azure?

Sim, esta configuração é suportada. Aqui estão os passos de alto nível para configurar este cenário:

* Implementar o agente de provisionamento #1 e registá-lo com o inquilino da AZure AD #1.
* Implementar o agente de provisionamento #2 e registá-lo com o inquilino da AZure AD #2.
* Com base nos "Domínios de Criança" que cada Agente De Provisionante irá gerir, configurar cada agente com o(s) domínio(s). Um agente pode lidar com vários domínios.
* No portal Azure, configurar a App de Provisionamento do Utilizador AD em cada inquilino e configurá-la com os respetivos domínios.

#### <a name="why-workday-to-azure-ad-user-provisioning-app-is-not-supported-if-we-have-deployed-azure-ad-connect"></a>Por que razão a aplicação de provisionamento do utilizador "Workday to Azure AD" não é suportada se implementamos o Azure AD Connect?

Quando o Azure AD é usado em modo híbrido (onde contém uma mistura de utilizadores em nuvem + no local), é importante ter uma definição clara de "fonte de autoridade". Os cenários tipicamente híbridos requerem a implantação do Azure AD Connect. Quando o Azure AD Connect é implantado, o AD no local é a fonte de autoridade. A introdução do conector AD Azure na mistura pode levar a uma situação em que os valores de atributos workday possam potencialmente substituir os valores definidos por Azure AD Connect. Assim, a utilização da aplicação de provisionamento "Workday to Azure AD" não é suportada quando o Azure AD Connect está ativado. Nestas situações, recomendamos a utilização da aplicação de provisionamento "Workday to AD User" para colocar os utilizadores no local AD e, em seguida, sincronizá-los em AZure AD usando Azure AD Connect.

#### <a name="how-do-i-suggest-improvements-or-request-new-features-related-to-workday-and-azure-ad-integration"></a>Como posso sugerir melhorias ou solicitar novas funcionalidades relacionadas com a integração do Workday e da AD Azure?

O seu feedback é altamente valorizado, pois ajuda-nos a definir o rumo para os lançamentos e melhorias futuras. Congratulamo-nos com todos os comentários e encorajamo-lo a submeter a sua ideia ou sugestão de melhoria no [fórum de feedback do AZure AD.](https://feedback.azure.com/forums/169401-azure-active-directory) Para obter feedback específico relacionado com a integração do Dia de Trabalho, selecione a categoria *Aplicações SaaS* e procure usando as palavras-chave *Workday* para encontrar feedback existente relacionado com o Dia de Trabalho.

> [!div class="mx-imgBorder"]
> ![Aplicativos UserVoice SaaS](media/workday-inbound-tutorial/uservoice_saas_apps.png)

> [!div class="mx-imgBorder"]
> ![Dia de Trabalho do UserVoice](media/workday-inbound-tutorial/uservoice_workday_feedback.png)

Ao sugerir uma nova ideia, verifique se alguém já sugeriu uma característica semelhante. Nesse caso, pode votar o recurso ou pedido de reforço. Também pode deixar um comentário sobre o seu caso de uso específico para mostrar o seu apoio à ideia e demonstrar como a funcionalidade também será valiosa para si.

### <a name="provisioning-agent-questions"></a>Questões do Agente de Provisionamento

#### <a name="what-is-the-ga-version-of-the-provisioning-agent"></a>Qual é a versão GA do Agente Provisionante?

Consulte o [Agente de Provisionamento AD AD Ad: O histórico](../app-provisioning/provisioning-agent-release-version-history.md) de lançamento da versão para a versão GA mais recente do Agente Provisionante.  

#### <a name="how-do-i-know-the-version-of-my-provisioning-agent"></a>Como conheço a versão do meu agente de provisionamento?

* Inscreva-se no servidor Windows onde o Agente de Provisionamento está instalado.
* Ir para **o Painel de Controlo**  ->  **Desinstalar ou Alterar um** menu de Programa
* Procure a versão correspondente à entrada **Microsoft Azure AD Connect Provisioning Agent**

  >[!div class="mx-imgBorder"]
  >![Portal do Azure](./media/workday-inbound-tutorial/pa_version.png)

#### <a name="does-microsoft-automatically-push-provisioning-agent-updates"></a>A Microsoft introduz automaticamente atualizações do Agente de Provisioning?

Sim, a Microsoft atualiza automaticamente o agente de provisionamento se o serviço do Windows **Microsoft Azure AD Connect Agent Updater** estiver em funcionamento.

#### <a name="can-i-install-the-provisioning-agent-on-the-same-server-running-azure-ad-connect"></a>Posso instalar o Agente de Provisioning no mesmo servidor que executa o Azure AD Connect?

Sim, pode instalar o Agente provisionante no mesmo servidor que executa o Azure AD Connect.

#### <a name="at-the-time-of-configuration-the-provisioning-agent-prompts-for-azure-ad-admin-credentials-does-the-agent-store-the-credentials-locally-on-the-server"></a>No momento da configuração, o Agente de Provisionamento solicita credenciais de administração Azure AD. O Agente armazena as credenciais localmente no servidor?

Durante a configuração, o Agente de Provisionamento solicita credenciais de administração Azure AD apenas para ligar ao seu inquilino AZure AD. Não armazena as credenciais localmente no servidor. No entanto, mantém as credenciais utilizadas para ligar ao *domínio ative do Diretório no local* num cofre de senha local do Windows.

#### <a name="how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication"></a>Como posso configurar o Agente Provisioning para utilizar um servidor proxy para comunicação HTTP de saída?

O Agente Provisioning apoia a utilização de procuração de saída. Pode configurgê-lo editando o ficheiro configurar o agente **C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\AADConnectProvisioningAgent.exe.config**. Adicione as seguintes linhas nele, no final do ficheiro, pouco antes da etiqueta de `</configuration>` fecho.
Substitua as variáveis [proxy-server] e [proxy-port] pelo nome do seu servidor proxy e pelos valores de porta.

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

#### <a name="how-do-i-ensure-that-the-provisioning-agent-is-able-to-communicate-with-the-azure-ad-tenant-and-no-firewalls-are-blocking-ports-required-by-the-agent"></a>Como posso assegurar que o Agente de Provisionamento seja capaz de comunicar com o inquilino Azure AD e que não haja firewalls a bloquear portas exigidas pelo agente?

Também pode verificar se todas as portas necessárias estão [abertas.](../manage-apps/application-proxy-add-on-premises-application.md#open-ports)

#### <a name="can-one-provisioning-agent-be-configured-to-provision-multiple-ad-domains"></a>Um agente de provisionamento pode ser configurado para a disponibilização de vários domínios de AD?

Sim, um agente de provisionamento pode ser configurado para lidar com vários domínios de AD, desde que o agente tenha linha de visão para os respetivos controladores de domínio. A Microsoft recomenda a criação de um grupo de 3 agentes de provisionamento que servem o mesmo conjunto de domínios AD para garantir uma alta disponibilidade e fornecer falhas no suporte.

#### <a name="how-do-i-de-register-the-domain-associated-with-my-provisioning-agent"></a>Como posso desscamur o domínio associado ao meu Agente de Provisionamento?

* Do portal Azure, obtenha a *identificação* do inquilino do seu inquilino Azure AD.
* Inscreva-se no servidor do Windows a executar o Agente de Provisionamento.
* Open PowerShell como administrador do Windows.
* Altere para o diretório que contém os scripts de registo e execute os seguintes comandos substituindo o parâmetro de identificação do \[ inquilino pelo valor da \] identificação do seu inquilino.

  ```powershell
  cd "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\RegistrationPowershell\Modules\PSModulesFolder"
  Import-Module "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\RegistrationPowershell\Modules\PSModulesFolder\AppProxyPSModule.psd1"
  Get-PublishedResources -TenantId "[tenant ID]"
  ```

* A partir da lista de agentes que aparecem – copie o valor do campo a `id` partir desse recurso cujo nome de *recurso* é igual ao nome de domínio da AD.
* Cole o valor de ID neste comando e execute o comando em PowerShell.

  ```powershell
  Remove-PublishedResource -ResourceId "[resource ID]" -TenantId "[tenant ID]"
  ```

* Reexame o assistente de configuração do Agente.
* Quaisquer outros agentes que foram previamente designados para este domínio terão de ser reconfigurados.

#### <a name="how-do-i-uninstall-the-provisioning-agent"></a>Como desinstalar o Agente de Provisionamento?

* Inscreva-se no servidor Windows onde o Agente de Provisionamento está instalado.
* Ir para **o Painel de Controlo**  ->  **Desinstalar ou Alterar um** menu de Programa
* Desinstalar os seguintes programas:
  * Microsoft Azure AD Connect Provisioning Agent
  * Microsoft Azure AD Connect Agent Updater
  * Microsoft Azure AD Connect Provisioning Agent Package

### <a name="workday-to-ad-attribute-mapping-and-configuration-questions"></a>Perguntas de mapeamento e configuração de atributos do dia de trabalho para AD

#### <a name="how-do-i-back-up-or-export-a-working-copy-of-my-workday-provisioning-attribute-mapping-and-schema"></a>Como posso fazer o back up ou exportar uma cópia de trabalho do meu Trabalhoday Provisioning Attribute Mapping e Schema?

Pode utilizar a Microsoft Graph API para exportar a sua configuração de Provisionamento do Utilizador do Dia de Trabalho. Consulte os passos na secção Exportação e Importação da [configuração deapeamento de atributos do utilizador do dia de trabalho](#exporting-and-importing-your-configuration) para obter mais detalhes.

#### <a name="i-have-custom-attributes-in-workday-and-active-directory-how-do-i-configure-the-solution-to-work-with-my-custom-attributes"></a>Tenho atributos personalizados no Workday e no Ative Directory. Como posso configurar a solução para trabalhar com os meus atributos personalizados?

A solução suporta atributos personalizados do Workday e ative Directory. Para adicionar os seus atributos personalizados ao esquema de mapeamento, abra a lâmina **de mapeamento** do Atributo e desloque-se para expandir a secção **Mostre opções avançadas**. 

![Editar Lista de Atributos](./media/workday-inbound-tutorial/wd_edit_attr_list.png)

Para adicionar os seus atributos personalizados workday, selecione a lista de atributos de *opção Editar para Workday* e para adicionar os seus atributos AD personalizados, selecione a lista de atributos de *Opção Editar para o Diretório Ativo nas Instalações*.

Veja também:

* [Personalizar a lista de atributos do utilizador workday](#customizing-the-list-of-workday-user-attributes)

#### <a name="how-do-i-configure-the-solution-to-only-update-attributes-in-ad-based-on-workday-changes-and-not-create-any-new-ad-accounts"></a>Como posso configurar a solução para apenas atualizar atributos em AD com base em alterações do Dia de Trabalho e não criar novas contas AD?

Esta configuração pode ser alcançada definindo as **Ações do Objeto Alvo** na lâmina **de mapeamento de atributos,** como mostrado abaixo:

![Atualizar ação](./media/workday-inbound-tutorial/wd_target_update_only.png)

Selecione a caixa de verificação "Update" para apenas atualizar operações para fluir de Workday para AD. 

#### <a name="can-i-provision-users-photo-from-workday-to-active-directory"></a>Posso providenciar a foto do utilizador do Workday ao Ative Directory?

A solução atualmente não suporta a definição de atributos binários, tais como *thumbnailPhoto* e *jpegPhoto* no Ative Directory.

#### <a name="how-do-i-sync-mobile-numbers-from-workday-based-on-user-consent-for-public-usage"></a>Como posso sincronizar os números de telemóvel do Workday com base no consentimento do utilizador para uso público?

* Vá à lâmina "Provisioning" da sua App de Provisionamento workday.
* Clique nos Mapeamentos de Atributos 
* Em **Mapeamentos**, selecione **Synchronize Workday Workers to On Premises Ative Directory** (ou **Synchronize Workday Workers to Azure AD).**
* Na página 'Mappings' De atributos, desloque-se para baixo e verifique a caixa "Mostrar Opções Avançadas".  Clique na **lista de atributos editar para Workday**
* Na lâmina que se abre, localiza o atributo "Mobile" e clica na linha para que possa editar o RGPD Móvel **de Expressão API** ![](./media/workday-inbound-tutorial/mobile_gdpr.png)

* Substitua a **Expressão API** pela nova expressão, que só recupera o número de telemóvel de trabalho se a "Bandeira de Uso Público" estiver definida como "True" no Workday.

    ```
     wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Contact_Data/wd:Phone_Data[translate(string(wd:Phone_Device_Type_Reference/@wd:Descriptor),'abcdefghijklmnopqrstuvwxyz','ABCDEFGHIJKLMNOPQRSTUVWXYZ')='MOBILE' and translate(string(wd:Usage_Data/wd:Type_Data/wd:Type_Reference/@wd:Descriptor),'abcdefghijklmnopqrstuvwxyz','ABCDEFGHIJKLMNOPQRSTUVWXYZ')='WORK' and string(wd:Usage_Data/@wd:Public)='1']/@wd:Formatted_Phone
    ```

* Guarde a Lista de Atributos.
* Guarde o Mapeamento do Atributo.
* Limpe o estado atual e reinicie a sincronização completa.

#### <a name="how-do-i-format-display-names-in-ad-based-on-the-users-departmentcountrycity-attributes-and-handle-regional-variances"></a>Como posso formatr os nomes em AD com base nos atributos do departamento/país/cidade do utilizador e lidar com as variações regionais?

É um requisito comum configurar o *atributo displayName* em AD, de modo a que também forneça informações sobre o departamento do utilizador e país/região. Por exemplo, se John Smith trabalha no Departamento de Marketing nos EUA, talvez queira que o seu nome de *exibição* apareça como *Smith, John (Marketing-US)*.

Eis como pode lidar com tais requisitos para construir *CN* ou *displayName* para incluir atributos como empresa, unidade de negócio, cidade ou país/região.

* Cada atributo Workday é recuperado usando uma expressão API XPATH subjacente, que é configurável em  **Atributo Mapping -> Secção Avançada -> Editar lista de atributos para Workday**. Aqui está a expressão padrão XPATH API para Workday *PreferredFirstName*, *PreferredLastName*, *Atributos de Organização de Empresa* e *Supervisão.*

     | Atributo workday | Expressão XPATH da API |
     | ----------------- | -------------------- |
     | Nome Preferido | wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Name_Data/wd:Preferred_Name_Data/wd:Name_Detail_Data/wd:First_Name/text() |
     | Nome preferido do Natal | wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Name_Data/wd:Preferred_Name_Data/wd:Name_Detail_Data/wd:Last_Name/text() |
     | Empresa | wd:Worker/wd:Worker_Data/wd:Organization_Data/wd:Worker_Organization_Data[wd:Organization_Data/wd:Organization_Type_Reference/wd:ID[='Organization_Type_ID']='Empresa'] @wd:type/wd:Organization_Reference/@wd:Descriptor |
     | SupervisãoOrganização | wd:Worker/wd:Worker_Data/wd:Organization_Data/wd:Worker_Organization_Data/wd:Organization_Data[wd:Organization_Type_Reference/wd:ID[='Organization_Type_ID']='Supervisory']/wd:Organization_Name/text[) @wd:type |
  
   Confirme com a sua equipa workday que a expressão API acima é válida para a configuração do seu inquilino workday. Se necessário, pode editá-los conforme descrito na secção [Personalizar a lista de atributos do utilizador do Workday](#customizing-the-list-of-workday-user-attributes).

* Da mesma forma, as informações sobre o país/região presentes no Workday são recuperadas utilizando o seguinte XPATH: *wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference*

     Existem 5 atributos relacionados com o país/região que estão disponíveis na secção de lista de atributos Workday.

     | Atributo workday | Expressão XPATH da API |
     | ----------------- | -------------------- |
     | Referência do país | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/wd:ID[ @wd:type ='ISO_3166-1_Alpha-3_Code']/text[) |
     | CountryReferenceFriendly | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/@wd:Descriptor |
     | PaísReferênciaSsumérico | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/wd:ID[='ISO_3166-1_Numeric-3_Code]/texto[) @wd:type |
     | CountryReferenceTwoLetter | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/wd:ID[ @wd:type ='ISO_3166-1_Alpha-2_Code']/texto[) |
     | Conferência CountryRegionReference | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Region_Reference/@wd:Descriptor |

  Confirme com a sua equipa workday que as expressões API acima são válidas para a configuração do seu inquilino workday. Se necessário, pode editá-los conforme descrito na secção [Personalizar a lista de atributos do utilizador do Workday](#customizing-the-list-of-workday-user-attributes).

* Para construir a expressão de mapeamento de atributos certos, identifique qual o atributo Workday "autoritariamente" que representa o primeiro nome do utilizador, apelido, país/região e departamento. Digamos que os atributos são *PreferredFirstName*, *PreferredLastName*, *CountryReferenceTwoLetter* e *SupervisyOrganization,* respectivamente. Pode usar isto para construir uma expressão para o *atributo ADName* como se segue para obter um nome de exibição como *Smith, John (Marketing-US)*.

    ```
     Append(Join(", ",[PreferredLastName],[PreferredFirstName]), Join(""," (",[SupervisoryOrganization],"-",[CountryReferenceTwoLetter],")"))
    ```
    Uma vez que tenha a expressão certa, edite a tabela De Mapeamentos de Atributos e modifique o mapeamento do atributo *displayName* como mostrado abaixo:   ![ DisplayName Mapping](./media/workday-inbound-tutorial/wd_displayname_map.png)

* Estendendo o exemplo acima, digamos que gostaria de converter nomes da cidade vindos do Workday em valores de abreviação e depois usá-lo para construir nomes de exibição como *Smith, John (CHI)* ou *Doe, Jane (NYC)*, então este resultado pode ser alcançado usando uma expressão Switch com o atributo *Workday Município* como a variável determinante.

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
  * [Sintaxe de função do switch](../app-provisioning/functions-for-customizing-application-data.md#switch)
  * [Junte-se à Sintaxe de Função](../app-provisioning/functions-for-customizing-application-data.md#join)
  * [Sintaxe de função de apêndice](../app-provisioning/functions-for-customizing-application-data.md#append)

#### <a name="how-can-i-use-selectuniquevalue-to-generate-unique-values-for-samaccountname-attribute"></a>Como posso utilizar o SelectUniqueValue para gerar valores únicos para o atributo samAccountName?

Digamos que pretende gerar valores únicos para o atributo *samAccountName* usando uma combinação de atributos *FirstName* e *LastName* do Workday. Dado abaixo está uma expressão que pode começar com:

```
SelectUniqueValue(
    Replace(Mid(Replace(NormalizeDiacritics(StripSpaces(Join("",  Mid([FirstName],1,1), [LastName]))), , "([\\/\\\\\\[\\]\\:\\;\\|\\=\\,\\+\\*\\?\\<\\>])", , "", , ), 1, 20), , "(\\.)*$", , "", , ),
    Replace(Mid(Replace(NormalizeDiacritics(StripSpaces(Join("",  Mid([FirstName],1,2), [LastName]))), , "([\\/\\\\\\[\\]\\:\\;\\|\\=\\,\\+\\*\\?\\<\\>])", , "", , ), 1, 20), , "(\\.)*$", , "", , ),
    Replace(Mid(Replace(NormalizeDiacritics(StripSpaces(Join("",  Mid([FirstName],1,3), [LastName]))), , "([\\/\\\\\\[\\]\\:\\;\\|\\=\\,\\+\\*\\?\\<\\>])", , "", , ), 1, 20), , "(\\.)*$", , "", , )
)
```

Como funciona a expressão acima: Se o utilizador é John Smith, primeiro tenta gerar JSmith, se o JSmith já existe, então gera JoSmith, se isso existe, gera JohSmith. A expressão também garante que o valor gerado satisfaz a restrição de comprimento e a restrição de caracteres especiais associada ao *samAccountName*.

Veja também:

* [Sintaxe de função média](../app-provisioning/functions-for-customizing-application-data.md#mid)
* [Substituir Sintaxe de função](../app-provisioning/functions-for-customizing-application-data.md#replace)
* [SelecioneUniqueValue Syntax](../app-provisioning/functions-for-customizing-application-data.md#selectuniquevalue)

#### <a name="how-do-i-remove-characters-with-diacritics-and-convert-them-into-normal-english-alphabets"></a>Como removo caracteres com diacríticos e converto-os em alfabetos ingleses normais?

Utilize a função [NormalizarDiacritics](../app-provisioning/functions-for-customizing-application-data.md#normalizediacritics) para remover caracteres especiais no primeiro nome e apelido do utilizador, enquanto constrói o endereço de e-mail ou valor CN para o utilizador.

## <a name="troubleshooting-tips"></a>Sugestões de resolução de problemas

Esta secção fornece orientações específicas sobre como resolver problemas de provisionamento com a sua integração workday utilizando os registos de auditoria AD AD do Azure e os registos do Visualizador de Eventos do Servidor do Windows. Baseia-se em cima dos passos e conceitos genéricos de resolução de problemas capturados no [Tutorial: Reportando sobre](../app-provisioning/check-status-user-account-provisioning.md) o fornecimento automático de conta de utilizador

Esta secção abrange os seguintes aspetos da resolução de problemas:

* [Configuração do Visualizador de Eventos do Windows para resolução de problemas do agente](#setting-up-windows-event-viewer-for-agent-troubleshooting)
* [Criação de registos de auditoria do portal Azure para resolução de problemas de serviço](#setting-up-azure-portal-audit-logs-for-service-troubleshooting)
* [Compreender registos para conta de utilizador AD criar operações](#understanding-logs-for-ad-user-account-create-operations)
* [Compreensão de registos para operações de atualização do Gestor](#understanding-logs-for-manager-update-operations)
* [Resolução de erros geralmente encontrados](#resolving-commonly-encountered-errors)

### <a name="setting-up-windows-event-viewer-for-agent-troubleshooting"></a>Configuração do Visualizador de Eventos do Windows para resolução de problemas do agente

* Inscreva-se na máquina do Servidor do Windows onde o Agente de Provisionamento é implantado
* Abra a aplicação de desktop **do Observador de Eventos do Windows Server.**
* Selecione **Registos do Windows > Aplicação**.
* Utilize o **registo de corrente do filtro...** opção de visualização de todos os eventos registados sob a fonte **AAD. Connect.ProvisioningAgent** e excluir eventos com o ID do evento "5", especificando o filtro "-5" como mostrado abaixo.

  ![Espectador de eventos do Windows](media/workday-inbound-tutorial/wd_event_viewer_01.png))

* Clique **em OK** e serdene a visualização do resultado pela coluna Data e **Hora.**

### <a name="setting-up-azure-portal-audit-logs-for-service-troubleshooting"></a>Criação de registos de auditoria do portal Azure para resolução de problemas de serviço

* Inicie o [portal Azure](https://portal.azure.com)e navegue para a secção de **registos** de auditoria da sua aplicação de provisionamento workday.
* Utilize o botão **Colunas** na página 'Registos de Auditoria' para exibir apenas as seguintes colunas na vista (Data, Atividade, Estado, Razão de Estado). Esta configuração garante que se foca apenas em dados que são relevantes para a resolução de problemas.

  ![Colunas de registo de auditoria](media/workday-inbound-tutorial/wd_audit_logs_00.png)

* Utilize os parâmetros de consulta **target** e **Date Range** para filtrar a vista. 
  * Desajei o parâmetro de consulta **target** ao "ID do Trabalhador" ou "ID do trabalhador" do objeto do trabalhador do workday.
  * Desa ajuste o **intervalo de datas** para um período de tempo adequado durante o qual pretende investigar por erros ou problemas com o provisionamento.

  ![Filtros de registo de auditoria](media/workday-inbound-tutorial/wd_audit_logs_01.png)

### <a name="understanding-logs-for-ad-user-account-create-operations"></a>Compreender registos para conta de utilizador AD criar operações

Quando é detetada uma nova contratação no Workday (digamos, com o ID do empregado *21023),* o serviço de fornecimento de AD Azure tenta criar uma nova conta de utilizador de AD para o trabalhador e no processo cria 4 registos de registos de auditoria, conforme descrito abaixo:

  [![O registo de auditoria cria operações](media/workday-inbound-tutorial/wd_audit_logs_02.png)](media/workday-inbound-tutorial/wd_audit_logs_02.png#lightbox)

Quando clica em qualquer um dos registos de registos de auditoria, a página Detalhes de **Atividade** abre-se. Aqui está o que a página Detalhes de **Atividade** apresenta para cada tipo de registo de registo.

* **Registo de importação do workday:** Este registo mostra a informação do trabalhador recolhida do Workday. Utilize informações na secção *Detalhes Adicionais* do registo de registo para resolver problemas com a recolha de dados do Workday. Um registo de exemplo é mostrado abaixo juntamente com ponteiros sobre como interpretar cada campo.

  ```JSON
  ErrorCode : None  // Use the error code captured here to troubleshoot Workday issues
  EventName : EntryImportAdd // For full sync, value is "EntryImportAdd" and for delta sync, value is "EntryImport"
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID (usually the Worker ID or Employee ID field)
  SourceAnchor : a071861412de4c2486eb10e5ae0834c3 // set to the WorkdayID (WID) associated with the record
  ```

* Registo de importação de **AD:** Este registo de registo mostra informações da conta recolhida a partir de AD. Como durante a criação inicial do utilizador não existe uma conta AD, a *Razão* do Estado da Atividade indicará que não foi encontrada nenhuma conta com o valor do atributo ID correspondente no Diretório Ativo. Utilize informações na secção *Detalhes Adicionais* do registo de registo para resolver problemas com a recolha de dados do Workday. Um registo de exemplo é mostrado abaixo juntamente com ponteiros sobre como interpretar cada campo.

  ```JSON
  ErrorCode : None // Use the error code captured here to troubleshoot Workday issues
  EventName : EntryImportObjectNotFound // Implies that object was not found in AD
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID
  ```

  Para encontrar registos de registo de agente de provisionamento correspondentes a esta operação de importação de AD, abra os registos do Visualizador de Eventos do Windows e utilize o **Find...** opção de menu para encontrar entradas de registo que contenham o valor do atributo ID/Propriedade de Junção (neste caso *21023).*

  ![Localizar](media/workday-inbound-tutorial/wd_event_viewer_02.png)

  Procure a entrada com *o Event ID = 9*, que lhe fornecerá o filtro de pesquisa LDAP utilizado pelo agente para recuperar a conta AD. Pode verificar se este é o filtro de pesquisa certo para recuperar entradas únicas do utilizador.

  ![Pesquisa LDAP](media/workday-inbound-tutorial/wd_event_viewer_03.png)

  O registo que imediatamente o segue com *o ID do Evento = 2* captura o resultado da operação de pesquisa e se devolveu quaisquer resultados.

  ![Resultados do LDAP](media/workday-inbound-tutorial/wd_event_viewer_04.png)

* Registo de **ação da regra de sincronização:** Este registo apresenta os resultados das regras de mapeamento do atributo e filtros de deteção configurados, juntamente com as medidas de provisionamento que serão tomadas para processar o evento workday que se aproxima. Utilize informações na secção *Detalhes Adicionais* do registo de registo para resolver problemas com a ação de sincronização. Um registo de exemplo é mostrado abaixo juntamente com ponteiros sobre como interpretar cada campo.

  ```JSON
  ErrorCode : None // Use the error code captured here to troubleshoot sync issues
  EventName : EntrySynchronizationAdd // Implies that the object will be added
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID
  SourceAnchor : a071861412de4c2486eb10e5ae0834c3 // set to the WorkdayID (WID) associated with the profile in Workday
  ```

  Se houver problemas com as expressões de mapeamento do seu atributo ou os dados do Dia de Trabalho que chegam têm problemas (por exemplo: valor vazio ou nulo para os atributos necessários), então observará uma falha nesta fase com o ErrorCode fornecendo detalhes da falha.

* **Registo de exportação de AD:** Este registo de registo apresenta o resultado da operação de criação de conta AD juntamente com os valores de atributos que foram definidos no processo. Utilize informações na secção *Detalhes Adicionais* do registo de registo para resolver problemas com a operação de criação de conta. Um registo de exemplo é mostrado abaixo juntamente com ponteiros sobre como interpretar cada campo. Na secção "Detalhes Adicionais", o "EventName" está definido para "EntryExportAdd", o "JoiningProperty" está definido para o valor do atributo ID correspondente, o "SourceAnchor" é definido para o WorkdayID (WID) associado ao registo e o "TargetAnchor" é definido para o valor do atributo "ObjectGuid" da AD do utilizador recém-criado. 

  ```JSON
  ErrorCode : None // Use the error code captured here to troubleshoot AD account creation issues
  EventName : EntryExportAdd // Implies that object will be created
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID
  SourceAnchor : a071861412de4c2486eb10e5ae0834c3 // set to the WorkdayID (WID) associated with the profile in Workday
  TargetAnchor : 83f0156c-3222-407e-939c-56677831d525 // set to the value of the AD "objectGuid" attribute of the new user
  ```

  Para encontrar registos de registo de agente de provisionamento correspondentes a esta operação de exportação de AD, abra os registos do Visualizador de Eventos do Windows e utilize o **Find...** opção de menu para encontrar entradas de registo que contenham o valor do atributo ID/Propriedade de Junção (neste caso *21023).*  

  Procure um registo HTTP POST correspondente ao selo de tempo da operação de exportação com *o Resultado ID = 2*. Este registo conterá os valores de atributos enviados pelo serviço de fornecimento ao agente de provisionamento.

  :::image type="content" source="media/workday-inbound-tutorial/wd_event_viewer_05.png" alt-text="Screenshot que mostra o registo 'HTTP POST' no registo 'Provisioning Agent'." lightbox="media/workday-inbound-tutorial/wd_event_viewer_05.png":::

  Imediatamente após o evento acima, deve haver outro evento que capte a resposta da operação de conta AD de criação. Este evento devolve o novo objetoGuid criado em AD e é definido como o atributo TargetAnchor no serviço de fornecimento.

  :::image type="content" source="media/workday-inbound-tutorial/wd_event_viewer_06.png" alt-text="Screenshot que mostra o registo 'HTTP POST' no registo 'Provisioning Agent'." lightbox="media/workday-inbound-tutorial/wd_event_viewer_06.png":::

### <a name="understanding-logs-for-manager-update-operations"></a>Compreensão de registos para operações de atualização de gestores

O atributo do gestor é um atributo de referência em AD. O serviço de prestação não define o atributo do gestor como parte da operação de criação de utilizadores. Em vez disso, o atributo do gestor é definido como parte de uma operação de *atualização* após a criação da conta AD para o utilizador. Expandindo o exemplo acima, digamos que uma nova contratação com ID de empregado "21451" é ativada no Workday e o novo gestor de aluguer (*21023)* já tem uma conta AD. Neste cenário, a pesquisa nos registos de Auditoria do utilizador 21451 mostra 5 entradas.

  [![Atualização do Gestor](media/workday-inbound-tutorial/wd_audit_logs_03.png)](media/workday-inbound-tutorial/wd_audit_logs_03.png#lightbox)

Os primeiros 4 discos são como os que exploramos como parte da operação de criação do utilizador. O 5º recorde é a exportação associada à atualização de atributos do gestor. O registo de registo apresenta o resultado da operação de atualização do gestor de conta AD, que é realizada utilizando o atributo *objectGuid* do gestor.

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

### <a name="resolving-commonly-encountered-errors"></a>Resolução de erros geralmente encontrados

Esta secção abrange erros geralmente vistos com o fornecimento de utilizadores do Workday e como resolvê-lo. Os erros são agrupados da seguinte forma:

* [Erros de agente de provisionamento](#provisioning-agent-errors)
* [Erros de conectividade](#connectivity-errors)
* [Erros de criação de conta de utilizador de AD](#ad-user-account-creation-errors)
* [Erros de atualização da conta de utilizador de AD](#ad-user-account-update-errors)

#### <a name="provisioning-agent-errors"></a>Erros de agente de provisionamento

|#|Cenário de erro |Causas Prováveis|Resolução Recomendada|
|--|---|---|---|
|1.| Erro na instalação do agente de provisionamento com mensagem de erro:  *O Serviço 'Microsoft Azure AD Connect Provisioning Agent' (AADConnectProvisioningAgent) não foi iniciado. Verifique se tem privilégios suficientes para iniciar o sistema.* | Este erro geralmente aparece se estiver a tentar instalar o agente de provisionamento num controlador de domínio e a política de grupo impede o início do serviço.  Também é visto se tem uma versão anterior do agente em execução e não o desinstalou antes de iniciar uma nova instalação.| Instale o agente de provisionamento num servidor não-DC. Certifique-se de que as versões anteriores do agente estão desinstaladas antes de instalar o novo agente.|
|2.| O Windows Service 'Microsoft Azure AD Connect Provisioning Agent' encontra-se no estado *inicial* e não muda para o estado *de funcionamento.* | Como parte da instalação, o assistente de agente cria uma conta local (**NT Service \\ AADConnectProvisioningAgent**) no servidor e esta é a conta de início de súm em dia útil utilizada para iniciar o serviço. Se uma política de segurança no seu servidor Windows impedir que as contas locais executem os serviços, encontrará este erro. | Abra a *consola Serviços.* Clique no botão direito no Serviço do Windows 'Microsoft Azure AD Connect Provisioning Agent' e no separador logon especificar a conta de um administrador de domínio para executar o serviço. Reinicie o serviço. |
|3.| Ao configurar o agente de provisionamento com o seu domínio AD no degrau *Connect Ative Directory*, o assistente demora muito tempo a tentar carregar o esquema de AD e, eventualmente, a esgotar-se. | Geralmente, este erro aparece se o assistente não conseguir contactar o servidor de controlador de domínio do AD devido a problemas na firewall. | No ecrã do assistente do *Diretório Ativo Connect,* ao mesmo tempo que fornece as credenciais para o seu domínio AD, existe uma opção chamada *Prioridade do controlador de domínio Select*. Utilize esta opção para selecionar um controlador de domínio que esteja no mesmo site que o servidor do agente e certifique-se de que não existem regras de firewall que bloqueiem a comunicação. |

#### <a name="connectivity-errors"></a>Erros de conectividade

Se o serviço de prestação não puder ligar-se ao Workday ou ao Ative Directory, poderá fazer com que o provisionamento entre em estado de quarentena. Utilize a tabela abaixo para resolver problemas de conectividade.

|#|Cenário de erro |Causas Prováveis|Resolução Recomendada|
|--|---|---|---|
|1.| Quando clica na **Ligação de Teste,** obtém a mensagem de erro: *Houve um erro de ligação ao Ative Directory. Certifique-se de que o Agente de Provisionamento no local está em funcionamento e está configurado com o domínio correto do Diretório Ativo.* | Este erro geralmente aparece se o agente de provisionamento não estiver em funcionamento ou se houver uma comunicação de bloqueio de firewall entre a Azure AD e o agente de provisionamento. Pode também ver este erro, se o domínio não estiver configurado no Assistente de Agente. | Abra a consola *de Serviços* no servidor Windows para confirmar que o agente está em funcionamento. Abra o assistente de provisionamento do agente e confirme que o domínio certo está registado com o agente.  |
|2.| O trabalho de provisionamento entra em estado de quarentena nos fins de semana (Fri-Sat) e recebemos uma notificação por e-mail de que há um erro com a sincronização. | Uma das causas comuns deste erro é o período de indisponibilidade planeado do Workday. Se estiver a utilizar um inquilino de implementação do Workday, tenha em conta que o Workday tem um período de indisponibilidade agendado para os inquilinos de implementação (normalmente, da noite de sexta-feira à manhã de sábado) e, durante esse período, as aplicações de aprovisionamento do Workday podem entrar em quarentena, pois não se conseguem ligar ao Workday. Regressam ao estado normal quando o inquilino de implementação do Workday estiver novamente online. Em casos raros, também poderá ver este erro se a palavra-passe de Utilizador de Sistema de Integração tiver sido alterada devido a uma atualização do inquilino ou se a conta estiver bloqueada ou expirada. | Verifique junto do seu administrador do Workday ou do seu parceiro de integração qual é o período de indisponibilidade do Workday, de modo a ignorar as mensagens de alerta durante esse período e confirmar a disponibilidade assim que a instância do Workday estiver de novo online.  |


#### <a name="ad-user-account-creation-errors"></a>Erros de criação de conta de utilizador de AD

|#|Cenário de erro |Causas Prováveis|Resolução Recomendada|
|--|---|---|---|
|1.| Falhas na operação de exportação no registo de auditoria com a mensagem *Erro: OperationsError-SvcErr: Ocorreu um erro de funcionamento. Não foi configurada qualquer referência superior para o serviço de diretório. O serviço de diretório é, portanto, incapaz de emitir referências a objetos fora desta floresta.* | Este erro geralmente aparece se o *Contentor do Diretório Ativo* OU não estiver corretamente definido ou se houver problemas com o Mapeamento de Expressão utilizado para o nome de *pais.* | Verifique o parâmetro OU do contentor do *diretório ativo* para obter tipografias. Se estiver a utilizar *parentDistinguishedName* no mapeamento de atributos, confirme que é sempre avaliado para um contentor conhecido dentro do domínio do AD. Consulte o evento *Exportação* nos registos de auditoria para ver o valor gerado. |
|2.| Falhas de operação de exportação no registo de auditoria com código de erro: *SystemForDomdomainDentityManagementBadResponse* e mensagem *Erro: RestriçãoViolation-AtrErr: Um valor no pedido é inválido. Um valor para o atributo não estava na gama aceitável de valores. \nError Detalhes: CONSTRAINT_ATT_TYPE - empresa*. | Embora este erro seja específico do atributo da *empresa,* pode ver este erro para outros atributos como *a CN* também. Este erro aparece devido à restrição de esquemas impostas pela AD. Por padrão, os atributos como *empresa* e *CN* em AD têm um limite superior de 64 caracteres. Se o valor proveniente do Workday for superior a 64 caracteres, então verá esta mensagem de erro. | Consulte o evento *Exportação* nos registos de auditoria para ver o valor do atributo reportado na mensagem de erro. Considere truncar o valor proveniente do Workday utilizando a função [Mid](../app-provisioning/functions-for-customizing-application-data.md#mid) ou alterar os mapeamentos para um atributo AD que não tenha restrições de comprimento semelhantes.  |

#### <a name="ad-user-account-update-errors"></a>Erros de atualização da conta de utilizador de AD

Durante o processo de atualização da conta de utilizador AD, o serviço de fornecimento lê informações tanto do Workday como da AD, executa as regras de mapeamento do atributo e determina se alguma alteração tem de entrar em vigor. Assim, um evento de atualização é desencadeado. Se algum destes passos encontrar uma falha, é registado nos registos de auditoria. Utilize a tabela abaixo para resolver erros comuns de atualização.

|#|Cenário de erro |Causas Prováveis|Resolução Recomendada|
|--|---|---|---|
|1.| Falhas de ação de regras de sincronização no registo de auditoria com a mensagem *EventName = EntrySynchronizationError e ErrorCode = EndpointUn disponível*. | Este erro aparece se o serviço de fornecimento não conseguir obter dados do perfil do utilizador do Ative Directory devido a um erro de processamento encontrado pelo agente de provisionamento no local. | Verifique os registos do Observador de Eventos do Agente de Provisioning para eventos de erro que indiquem problemas com a operação de leitura (Filter by Event ID #2). |
|2.| O atributo do gestor em AD não é atualizado para certos utilizadores em AD. | A causa mais provável deste erro é se estiver a utilizar regras de deteção e o gestor do utilizador não fizer parte do âmbito. Pode também encontrar-se com este problema se o atributo de ID correspondente do gestor (por exemplo, EmployeeID) não for encontrado no domínio de AD alvo ou não for definido para o valor correto. | Reveja o filtro de deteção e adicione o utilizador do gestor no âmbito. Verifique o perfil do gestor em AD para se certificar de que existe um valor para o atributo de ID correspondente. |

## <a name="managing-your-configuration"></a>Gerir a sua configuração

Esta secção descreve como pode estender, personalizar e gerir a configuração de provisionamento do utilizador orientada para o Workday. Abrange os seguintes tópicos:

* [Personalizar a lista de atributos do utilizador workday](#customizing-the-list-of-workday-user-attributes)  
* [Exportar e importar configuração](#exporting-and-importing-your-configuration)

### <a name="customizing-the-list-of-workday-user-attributes"></a>Personalizar a lista de atributos do utilizador workday

As aplicações de provisionamento workday para Ative Directory e Azure AD incluem uma lista padrão de atributos de utilizador do Workday que pode selecionar. No entanto, estas listas não são abrangentes. Workday suporta muitas centenas de possíveis atributos do utilizador, que podem ser standard ou exclusivos para o seu inquilino workday.

O serviço de fornecimento de Ad Azure suporta a capacidade de personalizar a sua lista ou atributo Workday para incluir quaisquer atributos expostos no [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) funcionamento da API de Recursos Humanos.

Para tal, tem de utilizar o [Workday Studio](https://community.workday.com/studio-download) para extrair as expressões XPath que representam os atributos que pretende utilizar e, em seguida, adicioná-las à sua configuração de provisionamento utilizando o editor de atributos avançado no portal Azure.

**Para recuperar uma expressão XPath para um atributo do utilizador workday:**

1. Descarregue e instale [o Workday Studio](https://community.workday.com/studio-download). Você precisará de uma conta comunitária workday para aceder ao instalador.

2. Descarregue o ficheiro do Workday **Human_Resources** WSDL específico para a versão API WWS que pretende utilizar a partir do [Workday Web Services Directory](https://community.workday.com/sites/default/files/file-hosting/productionapi/index.html)

3. Lançamento do Estúdio Workday.

4. A partir da barra de comando, selecione o Serviço Web de teste > workday na opção **Tester.**

5. Selecione **External**, e selecione o ficheiro Human_Resources WSDL que descarregou no passo 2.

    ![Screenshot que mostra o ficheiro "Human_Resources" aberto no Workday Studio.](./media/workday-inbound-tutorial/wdstudio1.png)

6. Desa cosçar o campo **de Localização** `https://IMPL-CC.workday.com/ccx/service/TENANT/Human_Resources` para , mas substituindo "IMPL-CC" pelo seu tipo de instância real, e "TENANT" pelo seu verdadeiro nome de inquilino.

7. Definir **operação** para **Get_Workers**

8.    Clique no pequeno link **de configuração** abaixo dos painéis de Pedido/Resposta para definir as suas credenciais de dia de trabalho. Verifique **autenticação**e, em seguida, introduza o nome de utilizador e a palavra-passe para a sua conta do sistema de integração workday. Certifique-se de que forma o nome de utilizador como inquilino do nome \@ e deixe a opção **Utilizador-Segurança WS-SecurityToken** selecionada.
   ![Screenshot que mostra o separador "Segurança" com o "Username" e "Password" introduzidos, e "WS-Security Username Token" selecionado.](./media/workday-inbound-tutorial/wdstudio2.png)

9. Selecione **OK**.

10. No painel **request,** cole no XML abaixo. Desaça **Employee_ID** para a identificação do empregado de um verdadeiro utilizador no seu inquilino workday. Desajei **a versão wd:versão** para a versão da WWS que pretende utilizar. Selecione um utilizador que tenha o atributo preenchido que deseja extrair.

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

11. Clique no **Pedido de Envio** (seta verde) para executar o comando. Se for bem sucedida, a resposta deve aparecer no painel **de resposta.** Verifique a resposta para garantir que tem os dados do ID do utilizador que introduziu, e não um erro.

12. Se for bem sucedido, copie o XML do painel **de resposta** e guarde-o como um ficheiro XML.

13. Na barra de comando do Workday Studio, selecione **Ficheiro > Ficheiro Aberto...** e abra o ficheiro XML que guardou. Esta ação abrirá o ficheiro no editor do Workday Studio XML.

    ![Screenshot de um ficheiro X M L aberto no "Workday Studio X M L editor".](./media/workday-inbound-tutorial/wdstudio3.png)

14. Na árvore de arquivo, navegue através **de /env: Envelope > env: Body > wd:Get_Workers_Response > wd:Response_Data > wd: Trabalhador** para encontrar os dados do seu utilizador.

15. Em **WD: Trabalhador,** encontre o atributo que deseja adicionar e selecione-o.

16. Copie a expressão XPath para o seu atributo selecionado para fora do campo Caminho do **Documento.**

17. Retire o **/env:Envelope/env:Body/wd:Get_Workers_Response/wd:Response_Data/** prefixo da expressão copiada.

18. Se o último item na expressão copiada for um nó (exemplo: "/wd: Birth_Date"), então apêndice **/texto()** no final da expressão. Isto não é necessário se o último item for um atributo (exemplo: " /@wd: tipo").

19. O resultado deve ser algo `wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()` como. Este valor é o que irá copiar no portal Azure.

**Para adicionar o seu atributo personalizado do utilizador Workday à sua configuração de provisionamento:**

1. Lance o [portal Azure](https://portal.azure.com)e navegue para a secção de Provisionamento da sua aplicação de provisionamento workday, como descrito anteriormente neste tutorial.

2. Descoda **o estado de provisionamento** **e**selecione **Guardar**. Este passo ajudará a garantir que as suas alterações só produzirão efeitos quando estiver pronto.

3. Em **Mapeamentos**, selecione **Synchronize Workday Workers to On Premises Ative Directory** (ou **Synchronize Workday Workers to Azure AD).**

4. Percorra a parte inferior do ecrã seguinte e selecione **Mostrar opções avançadas**.

5. **Selecione a lista de atributos editar para Workday**.

    ![Screenshot que mostra a página "Workday to Azure A D User Provisioning - Provisioning" com a ação "Editar a lista de atributos para o Dia de Trabalho" realçada.](./media/workday-inbound-tutorial/wdstudio_aad1.png)

6. Percorra para a parte inferior da lista de atributos até onde estão os campos de entrada.

7. Para **nome,** introduza um nome de exibição para o seu atributo.

8. Para **tipo**, selecione o tipo que corresponde adequadamente ao seu atributo **(A corda** é mais comum).

9. Para **API Expression,** insira a expressão XPath que copiou do Workday Studio. Exemplo: `wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()`

10. Selecione **Adicionar Atributo**.

    ![Estúdio Workday](./media/workday-inbound-tutorial/wdstudio_aad2.png)

11. **Selecione Guardar** acima e, em seguida, **Sim** para o diálogo. Feche o ecrã Attribute-Mapping se ainda estiver aberto.

12. De volta ao separador de **Provisionamento** principal, selecione **Synchronize Workday Workers to On Premises Ative Directory** (ou **Synchronize Workers to Azure AD**) novamente.

13. **Selecione Adicionar novo mapeamento**.

14. O seu novo atributo deve agora aparecer na lista **de atributos Source.**

15. Adicione um mapeamento para o seu novo atributo como desejado.

16. Quando terminar, lembre-se de definir **o Estado de Provisionamento** de volta para **On** e salvar.

### <a name="exporting-and-importing-your-configuration"></a>Exportar e importar configuração

Consulte a [configuração do artigo Exportação e importação de provisionamento](../app-provisioning/export-import-provisioning-configuration.md)

## <a name="managing-personal-data"></a>Gerir dados pessoais

A solução de provisionamento do Workday para o Ative Directory requer que um agente de provisionamento seja instalado num servidor Windows no local, e este agente cria registos no registo do Evento do Windows que podem conter dados pessoais dependendo do seu dia de trabalho para mapeamentos de atributos AD. Para cumprir as obrigações de privacidade do utilizador, pode garantir que nenhum dado seja retido nos registos do Evento para além de 48 horas, configurando uma tarefa agendada para o Windows para limpar o registo do evento.

O serviço de fornecimento de Azure AD enquadra-se na categoria de processador de **dados** da classificação do RGPD. Como um pipeline de processador de dados, o serviço fornece serviços de processamento de dados a parceiros-chave e consumidores finais. O serviço de fornecimento de AD AZure não gera dados de utilizadores e não tem controlo independente sobre os dados pessoais recolhidos e como são utilizados. A recuperação de dados, agregação, análise e reporte no serviço de fornecimento de Azure AD baseiam-se em dados empresariais existentes.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-hybrid-note.md)]

No que diz respeito à retenção de dados, o serviço de fornecimento de Ad Azure não gera relatórios, realiza análises ou fornece insights para além de 30 dias. Por isso, o serviço de fornecimento AZURE AD não armazena, processa ou retém quaisquer dados para além de 30 dias. Este design está em conformidade com os regulamentos do RGPD, os regulamentos de conformidade com a privacidade da Microsoft e as políticas de retenção de dados AZure.

## <a name="next-steps"></a>Passos seguintes

* [Saiba como analisar os registos e obter relatórios sobre a atividade de aprovisionamento](../app-provisioning/check-status-user-account-provisioning.md)
* [Saiba como configurar um único sign-on entre workday e Azure Ative Directory](workday-tutorial.md)
* [Saiba como integrar outras aplicações saaS com o Azure Ative Directory](tutorial-list.md)
* [Saiba como utilizar as APIs do Microsoft Graph para gerir configurações de provisionamento](/graph/api/resources/synchronization-overview)