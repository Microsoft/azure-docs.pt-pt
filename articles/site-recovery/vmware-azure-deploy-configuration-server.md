---
title: Implementar o servidor de configuração na Recuperação do Site Azure
description: Este artigo descreve como implementar um servidor de configuração para recuperação de desastres VMware com recuperação do site Azure
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/15/2019
ms.author: ramamill
ms.openlocfilehash: a8ceb3df68ebe42f83c70ed62327bf59c0dfc225
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92359820"
---
# <a name="deploy-a-configuration-server"></a>Implementar um servidor de configuração

Implementa um servidor de configuração no local quando utiliza [a Recuperação do Site Azure](site-recovery-overview.md) para a recuperação de desastres de VMware VMs e servidores físicos para o Azure. O servidor de configuração coordena as comunicações entre o VMware e o Azure no local. Também gere a replicação de dados. Este artigo acompanha-o através dos passos necessários para implantar o servidor de configuração quando está a replicar VMware VMs para Azure. Se precisar de configurar um servidor de configuração para a replicação do servidor físico, consulte [configurar o servidor de configuração para a recuperação de desastres dos servidores físicos para o Azure](physical-azure-set-up-source.md).

> [!TIP]
> Para conhecer o papel de um servidor de configuração como parte da arquitetura de recuperação do local de Azure, consulte a arquitetura de recuperação de [desastres VMware to Azure](vmware-azure-architecture.md).

## <a name="deploy-a-configuration-server-through-an-ova-template"></a>Implementar um servidor de configuração através de um modelo OVA

O servidor de configuração deve ser configurado como um VMware VM altamente disponível com determinados requisitos mínimos de hardware e dimensionamento. Para uma implementação conveniente e fácil, a Recuperação do Site fornece um modelo de Aplicação de Virtualização Aberta (OVA) transferível para configurar o servidor de configuração que cumpre todos os requisitos obrigatórios listados aqui.

## <a name="prerequisites"></a>Pré-requisitos

Os requisitos mínimos de hardware para um servidor de configuração são resumidos nas seguintes secções.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="azure-active-directory-permission-requirements"></a>Requisitos de permissão do Diretório Ativo Azure

Deve ter um utilizador com uma das seguintes permissões definidas no Azure Ative Directory (Azure AD) para registar o servidor de configuração com os serviços de Recuperação do Site Azure.

1. O utilizador deve ter uma função de desenvolvedor de aplicações para criar uma aplicação.
    - Para verificar, inscreva-se no portal Azure.</br>
    - Vá ao **Azure Ative Directory**  >  **Roles e administradores**.</br>
    - Verifique se a função de desenvolvedor de aplicações é atribuída ao utilizador. Caso contrário, utilize um utilizador com esta permissão ou contacte um [administrador para ativar a permissão](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md#assign-roles).
    
2. Se a função de desenvolvedor de aplicações não puder ser atribuída, certifique-se de que o registo da bandeira das **aplicações** é definido como **verdadeiro** para o utilizador criar uma identidade. Para ativar estas permissões:
    - Inicie sessão no portal do Azure.
    - Aceda às definições do Utilizador **do Diretório Ativo Azure**  >  .
    - Nos **registos da App,** **os utilizadores podem registar aplicações,** selecione **Sim.**

      ![Azure AD_application_permission](media/vmware-azure-deploy-configuration-server/AAD_application_permission.png)

> [!NOTE]
> Os Serviços da Federação De Diretório Ativo *não são apoiados.* Utilize uma conta gerida através do [Azure Ative Directory](../active-directory/fundamentals/active-directory-whatis.md).

## <a name="download-the-template"></a>Transferir o modelo

1. No cofre, vá para preparar a **fonte de infraestrutura.**  >  
2. Em **Preparar origem**, selecione **+Servidor de configuração**.
3. Em **Adicionar Servidor**, verifique se o **Servidor de configuração para VMware** é apresentado no **Tipo de servidor**.
4. Descarregue o modelo OVA para o servidor de configuração.

   > [!TIP]
   >Também pode descarregar a versão mais recente do modelo de servidor de configuração diretamente do [Microsoft Download Center](https://aka.ms/asrconfigurationserver).

> [!NOTE]
> A licença fornecida com um modelo de OVA é uma licença de avaliação válida por 180 dias. Após este período, deve obter uma licença.

## <a name="import-the-template-in-vmware"></a>Importar o modelo no VMware

1. Inscreva-se no servidor VMware vCenter ou no vSphere ESXi, utilizando o VMware vSphere Client.
2. No menu **'Ficheiro',** selecione **implementar o modelo OVF** para iniciar o assistente **de modelo de implementação OVF.**

     ![Implementar modelo de OVF](./media/vmware-azure-deploy-configuration-server/vcenter-wizard.png)

3. Em **Selecionar origem**, introduza a localização do OVF transferido.
4. Em **Rever detalhes**, selecione **Seguinte**.
5. Em **Selecionar nome e pasta** e **Selecionar configuração**, aceite as predefinições.
6. Em **Selecionar armazenamento**, para obter o melhor desempenho, selecione **Thick Provision Eager Zeroed** em **Selecionar formato de disco virtual**. A utilização da opção de provisionamento fino pode afetar o desempenho do servidor de configuração.
7. Nas restantes páginas do assistente, aceite as predefinições.
8. Em **Pronto para concluir**:

    * Para configurar o VM com as definições predefinidos, selecione **Ligar a ligar após a colocação**  >  **Terminar**.
    * Para adicionar uma interface de rede adicional, **limpe a energia após a implementação** e, em seguida, selecione **Terminar**. Por predefinição, o modelo do servidor de configuração é implementado com um único NIC. Pode adicionar mais NICs após a implementação.

> [!IMPORTANT]
> Não altere as configurações de recursos, tais como memória, núcleos e restrição de CPU, ou modifique ou elimine serviços ou ficheiros instalados no servidor de configuração após a implementação. Estes tipos de alterações afetam o registo do servidor de configuração com os serviços Azure e o desempenho do servidor de configuração.

## <a name="add-an-additional-adapter"></a>Adicionar outro adaptador

> [!NOTE]
> São necessários dois NICs se pretender reter os endereços IP das máquinas de origem em falha e pretender voltar ao local mais tarde. Um NIC está ligado a máquinas de origem, e o outro NIC é usado para conectividade Azure.

Se pretender adicionar um NIC adicional ao servidor de configuração, adicione-o antes de registar o servidor no cofre. Adicionar mais adaptadores não é suportado após o registo.

1. No inventário do vSphere Client, clique na VM com o botão direito do rato e selecione **Editar Definições**.
2. Em **Hardware**, selecione **Adicionar** > **Adaptador Ethernet**. Em seguida, selecione **Seguinte**.
3. Selecione um tipo de adaptador e uma rede.
4. Para ligar o NIC virtual quando o VM estiver ligado, selecione **Connect on-on**. Em seguida, selecione **Next**  >  **Finish**  >  **OK**.

## <a name="register-the-configuration-server-with-azure-site-recovery-services"></a>Registar o servidor de configuração com os serviços de Recuperação do Site Azure

1. A partir da consola VMware vSphere Client, ligue o VM.
2. A VM arranca para uma experiência de instalação do Windows Server 2016. Aceite o contrato de licença e introduza uma palavra-passe de administrador.
3. Após a conclusão da instalação, inicie sessão na VM como administrador.
4. A primeira vez que iniciar seduca, dentro de poucos segundos, a ferramenta de configuração de recuperação do local Azure começa.
5. Introduza um nome que será utilizado para registar o servidor de configuração no Site Recovery. Em seguida, selecione **Seguinte**.
6. A ferramenta verifica se a VM pode ligar ao Azure. Depois de a ligação estar estabelecida, selecione **Iniciar sessão** para iniciar sessão na sua subscrição do Azure.</br>
    a. As credenciais têm de ter acesso ao cofre no qual pretende registar o servidor de configuração.</br>
    b. Certifique-se de que a conta de utilizador escolhida tem permissão para criar uma aplicação no Azure. Para ativar as permissões necessárias, siga as diretrizes da secção [Azure Ative Directory requirements](#azure-active-directory-permission-requirements).
7. A ferramenta executa algumas tarefas de configuração e, em seguida, é reiniciada.
8. Inicie sessão na máquina novamente. O assistente de gestão do servidor de configuração começa automaticamente em poucos segundos.

### <a name="configure-settings"></a>Configurar definições

1. No assistente de gestão do servidor de configuração, selecione **a conectividade de configuração**. A partir das caixas de entrega, selecione primeiro o NIC que o servidor de processo incorporado utiliza para a descoberta e instalação de serviço de mobilidade em máquinas de origem. Em seguida, selecione o NIC que o servidor de configuração utiliza para conectividade com Azure. Selecione **Guardar**. Não é possível alterar esta definição depois de estar configurada. Não altere o endereço IP de um servidor de configuração. Certifique-se de que o IP atribuído ao servidor de configuração é um IP estático e não um IP DHCP.
2. No **cofre Select Recovery Services**, inscreva-se no Microsoft Azure com as credenciais utilizadas no passo 6 do Registo do servidor de [configuração com os serviços de Recuperação do Local Azure](#register-the-configuration-server-with-azure-site-recovery-services).
3. Após o sômin, selecione a sua subscrição Azure e o grupo de recursos relevantes e o cofre.

    > [!NOTE]
    > Após o registo, não há flexibilidade para mudar o cofre dos serviços de recuperação.
    > A alteração de um cofre de serviços de recuperação requer a desassociação do servidor de configuração do cofre atual, e a replicação de todas as máquinas virtuais protegidas sob o servidor de configuração é interrompida. Para saber mais, consulte [Gerir o servidor de configuração para a recuperação de desastres VMware VMware](vmware-azure-manage-configuration-server.md#register-a-configuration-server-with-a-different-vault).

4. Na **Instalação de software de terceiros:**

    |Scenario   |Passos a seguir  |
    |---------|---------|
    |Posso descarregar e instalar o MySQL manualmente?     |  Sim. Descarregue a aplicação MySQL, coloque-a na pasta **C:\Temp\ASRSetup** e, em seguida, instale manualmente. Depois de aceitar os termos e selecionar **Descarregar e instalar,** o portal diz *que já está instalado.* Pode passar ao próximo passo.       |
    |Posso evitar o download do MySQL online?     |   Sim. Coloque a aplicação do instalador MySQL na pasta **C:\Temp\ASRSetup**. Aceite os termos, selecione **Descarregar e instalar,** e o portal utiliza o instalador que adicionou para instalar a aplicação. Após o fim da instalação, proceda ao passo seguinte.    |
    |Quero descarregar e instalar o MySQL através da Recuperação do Site Azure.    |  Aceite o contrato de licença e selecione **Baixar e instalar.** Após o fim da instalação, proceda ao passo seguinte.       |

5. Na **configuração do aparelho validado,** os pré-requisitos são verificados antes de continuar.
6. No **servidor Configure vCenter Server/vSphere ESXi,** insira o endereço FQDN ou IP do servidor vCenter, ou vSphere host, onde estão localizados os VMs que pretende replicar. Introduza a porta em que o servidor está a escutar. Introduza um nome amigável a utilizar para o servidor VMware no cofre.
7. Introduza as credenciais que o servidor de configuração irá utilizar para ligar ao servidor VMware. O Site Recovery utiliza estas credenciais para detetar automaticamente as VMs VMware que estão disponíveis para replicação. **Selecione**  >  **Adicionar Continuar**. As credenciais aqui inscritas são guardadas localmente.
8. Nas **credenciais de máquina virtual configurar,** insira o nome de utilizador e a palavra-passe das máquinas virtuais para instalar automaticamente o serviço de mobilidade durante a replicação. Para as máquinas **Windows,** a conta necessita de privilégios de administrador local nas máquinas que pretende replicar. Para **Linux,** forneça detalhes para a conta raiz.
9. Selecione **Finalizar configuração** para concluir o registo.
10. Após o início do registo, abra o portal Azure e verifique se o servidor de configuração e o servidor VMware estão listados nos **Servidores** de Configuração da Infraestrutura de  >    >  **Recuperação do Cofre** do Cofre  >  de Recuperação do Cofre .

## <a name="upgrade-the-configuration-server"></a>Atualizar o servidor de configuração

Para atualizar o servidor de configuração para a versão mais recente, consulte [Gerir o servidor de configuração para a recuperação de desastres VMware VM](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server). Para obter instruções sobre como atualizar todos os componentes da Recuperação do Site, consulte [as atualizações do Serviço na Recuperação do Local.](service-updates-how-to.md)

## <a name="manage-the-configuration-server"></a>Gerir o servidor de configuração

Para evitar interrupções na replicação em curso, certifique-se de que o endereço IP do servidor de configuração não muda após o servidor de configuração ser registado num cofre. Para saber mais sobre as tarefas comuns de gestão do servidor de configuração, consulte [Gerir o servidor de configuração para a recuperação de desastres VMware VM](vmware-azure-manage-configuration-server.md).

## <a name="troubleshoot-deployment-issues"></a>Resolver problemas de implementação

Consulte o nosso [artigo de resolução de problemas](vmware-azure-troubleshoot-configuration-server.md) para resolver a implementação & problemas de conectividade.

## <a name="faqs"></a>FAQs

* Quanto tempo a licença fornecida num servidor de configuração é implementada através do OVF válido? O que acontece se eu não reativar a licença?

    A licença fornecida com um modelo de OVA é uma licença de avaliação válida por 180 dias. Antes de expirar, precisa ativar a licença. Caso contrário, pode resultar num encerramento frequente do servidor de configuração e causar um obstáculo às atividades de replicação. Para obter mais informações, consulte [Gerir o servidor de configuração para a recuperação de desastres VMware VMware](vmware-azure-manage-configuration-server.md#update-windows-license).

* Posso utilizar o VM onde o servidor de configuração está instalado para diferentes finalidades?

    N.º Utilize o VM para o único fim do servidor de configuração. Certifique-se de que segue todas as especificações mencionadas nos [Pré-requisitos](#prerequisites) para uma gestão eficiente da recuperação de desastres.
* Posso trocar o cofre já registado no servidor de configuração com um cofre recém-criado?

    N.º Depois de um cofre ser registado no servidor de configuração, não pode ser alterado.
* Posso usar o mesmo servidor de configuração para proteger máquinas físicas e virtuais?

    Sim. O mesmo servidor de configuração pode ser usado para replicar máquinas físicas e virtuais. No entanto, a máquina física só pode ser ressartada para um VMware VM.
* Qual é o propósito de um servidor de configuração e onde é usado?

    Para saber mais sobre o servidor de configuração e as suas funcionalidades, consulte [a arquitetura de replicação VMware para Azure](vmware-azure-architecture.md).
* Onde posso encontrar a versão mais recente do servidor de configuração?

    Para obter passos para atualizar o servidor de configuração através do portal, consulte [atualizar o servidor de configuração](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server). Para obter instruções sobre como atualizar todos os componentes da Recuperação do Site, consulte [as atualizações do Serviço na Recuperação do Local.](./service-updates-how-to.md)
* Onde posso descarregar a palavra-passe para o servidor de configuração?

    Para descarregar a palavra-passe, consulte [Gerir o servidor de configuração para obter a recuperação de desastres VMware VM](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase).
* Posso mudar a frase?

    N.º Não altere a palavra-passe do servidor de configuração. Uma mudança na palavra-passe quebra a replicação de máquinas protegidas e leva a um estado crítico de saúde.
* Onde posso baixar as chaves de registo do cofre?

    No **Cofre de Serviços de Recuperação**, selecione **Gerir**  >  servidores de configuração da **infraestrutura de**  >  **recuperação do local**. Em **Servers**, selecione Baixar a **chave de registo** para descarregar o ficheiro de credenciais do cofre.
* Posso clonar um servidor de configuração existente e usá-lo para orquestração de replicação?

    N.º A utilização de um componente do servidor de configuração clonado não é suportada. A clonagem de um servidor de processo de escala também é um cenário não suportado. Os componentes de recuperação do local de clonagem afetam as replicações em curso.

* Posso alterar o IP de um servidor de configuração?

    N.º Não altere o endereço IP de um servidor de configuração. Certifique-se de que todos os IPs atribuídos ao servidor de configuração são IPs estáticos e não IPs DHCP.
* Posso configurar um servidor de configuração no Azure?

    Configurar um servidor de configuração num ambiente no local com uma linha de visão direta com v-Center e para minimizar as latências de transferência de dados. Pode realizar cópias de segurança programadas do servidor de configuração para [fins de falha](vmware-azure-manage-configuration-server.md#failback-requirements).

* Posso alterar o controlador de cache num servidor de configuração ou servidor de processo de escala?

    Não, o controlador cache não pode ser alterado uma vez que a configuração esteja completa.

Para obter mais PERGUNTAS Frequentes nos servidores de [configuração, consulte as perguntas comuns do servidor de configuração](vmware-azure-common-questions.md#configuration-server).

## <a name="next-steps"></a>Passos seguintes

Confiúdo de recuperação de [VMware VMs](vmware-azure-tutorial.md) para Azure.