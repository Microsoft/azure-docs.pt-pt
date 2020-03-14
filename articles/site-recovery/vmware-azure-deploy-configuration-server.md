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
ms.openlocfilehash: 5209dab5e0934cc98bb1334a1565cc13998a7d2e
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79257384"
---
# <a name="deploy-a-configuration-server"></a>Implementar um servidor de configuração

Implementa um servidor de configuração no local quando utiliza a Recuperação do [Site Azure](site-recovery-overview.md) para a recuperação de desastres de VMware VMs e servidores físicos para o Azure. O servidor de configuração coordena as comunicações entre vMware no local e Azure. Também gere a replicação de dados. Este artigo acompanha-o através dos passos necessários para implementar o servidor de configuração quando estiver a replicar VMware VMs para o Azure. Se precisar de configurar um servidor de configuração para a replicação do servidor físico, consulte Configurar o servidor de [configuração para a recuperação de desastres de servidores físicos para o Azure](physical-azure-set-up-source.md).

> [!TIP]
> Para conhecer o papel de um servidor de configuração como parte da arquitetura azure site recovery, consulte VMware para a arquitetura de recuperação de [desastres de Azure.](vmware-azure-architecture.md)

## <a name="deploy-a-configuration-server-through-an-ova-template"></a>Implementar um servidor de configuração através de um modelo OVA

O servidor de configuração deve ser configurado como um VMware VM altamente disponível com certos requisitos mínimos de hardware e dimensionamento. Para uma implementação conveniente e fácil, a Recovery do Site fornece um modelo de aplicação de virtualização aberta (OVA) descarregado para configurar o servidor de configuração que cumpre todos os requisitos exigidos aqui.

## <a name="prerequisites"></a>Pré-requisitos

Os requisitos mínimos de hardware para um servidor de configuração são resumidos nas seguintes secções.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="azure-active-directory-permission-requirements"></a>Requisitos de permissão de diretório ativo azure

Deve ter um utilizador com uma das seguintes permissões definidas no Azure Ative Directory (Azure AD) para registar o servidor de configuração com os serviços de Recuperação do Site Azure.

1. O utilizador deve ter uma função de desenvolvedor de aplicações para criar uma aplicação.
    - Para verificar, inscreva-se no portal Azure.</br>
    - Vá ao **Diretório Ativo da Azure** > **Funções e administradores.**</br>
    - Verifique se a função de desenvolvedor de aplicações é atribuída ao utilizador. Caso contrário, utilize um utilizador com esta permissão ou contacte um [administrador para permitir a permissão](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal#assign-roles).
    
2. Se a função de desenvolvedor de aplicações não puder ser atribuída, certifique-se de que o Utilizador pode registar a bandeira das **aplicações** é definida como **verdadeira** para o utilizador criar uma identidade. Para permitir estas permissões:
    - Inicie sessão no Portal do Azure.
    - Vá ao **Diretório Ativo do Azure** > **definições de utilizador**.
    - Nos **registos da App,** **os utilizadores podem registar aplicações,** selecionar **Sim.**

      ![AD_application_permission Azul](media/vmware-azure-deploy-configuration-server/AAD_application_permission.png)

> [!NOTE]
> Os Serviços da Federação de DiretórioS Ativos *não são apoiados.* Utilize uma conta gerida através do [Diretório Ativo Azure.](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)

## <a name="download-the-template"></a>Transferir o modelo

1. No cofre, vá para **Preparar Infraestrutura** > **Origem**.
2. Em **Preparar origem**, selecione **+Servidor de configuração**.
3. Em **Adicionar Servidor**, verifique se o **Servidor de configuração para VMware** é apresentado no **Tipo de servidor**.
4. Descarregue o modelo OVA para o servidor de configuração.

   > [!TIP]
   >Também pode descarregar a versão mais recente do modelo de servidor de configuração diretamente do [Microsoft Download Center](https://aka.ms/asrconfigurationserver).

> [!NOTE]
> A licença fornecida com um modelo OVA é uma licença de avaliação válida por 180 dias. Após este período, deve obter uma licença.

## <a name="import-the-template-in-vmware"></a>Importar o modelo no VMware

1. Inicie sessão no servidor VMware vCenter ou no anfitrião vSphere ESXi, utilizando o VMWare vSphere Client.
2. No menu **'Ficheiro',** selecione Implementar o **modelo OVF** para iniciar o assistente de **modelo OVF de implantação.**

     ![Implementar o modelo OVF](./media/vmware-azure-deploy-configuration-server/vcenter-wizard.png)

3. Em **Selecionar origem**, introduza a localização do OVF transferido.
4. Em **Rever detalhes**, selecione **Seguinte**.
5. Em **Selecionar nome e pasta** e **Selecionar configuração**, aceite as predefinições.
6. Em **Selecionar armazenamento**, para obter o melhor desempenho, selecione **Thick Provision Eager Zeroed** em **Selecionar formato de disco virtual**. A utilização da opção de provisionamento fino pode afetar o desempenho do servidor de configuração.
7. Nas restantes páginas do assistente, aceite as predefinições.
8. Em **Pronto para concluir**:

    * Para configurar a VM com as predefinições, selecione **Ligar após a implementação** > **Concluir**.
    * Para adicionar uma interface de rede adicional, limpe **a alimentação após a implementação**, e, em seguida, selecione **Terminar**. Por predefinição, o modelo do servidor de configuração é implementado com um único NIC. Pode adicionar mais NICs após a implementação.

> [!IMPORTANT]
> Não altere as configurações de recursos, tais como a restrição de memória, núcleos e CPU, ou modifique ou elimine serviços ou ficheiros instalados no servidor de configuração após a implementação. Este tipo de alterações afetam o registo do servidor de configuração com os serviços Azure e o desempenho do servidor de configuração.

## <a name="add-an-additional-adapter"></a>Adicionar outro adaptador

> [!NOTE]
> São necessários dois NICs se pretender reter os endereços IP das máquinas de origem no failover e pretender não voltar ao local mais tarde. Um NIC está ligado a máquinas de origem, e o outro NIC é usado para conectividade Azure.

Se pretender adicionar um NIC adicional ao servidor de configuração, adicione-o antes de registar o servidor no cofre. Adicionar mais adaptadores não é suportado após o registo.

1. No inventário do vSphere Client, clique na VM com o botão direito do rato e selecione **Editar Definições**.
2. Em **Hardware**, selecione **Adicionar** > **Adaptador Ethernet**. Em seguida, selecione **Seguinte**.
3. Selecione um tipo de adaptador e uma rede.
4. Para ligar o NIC virtual quando o VM estiver ligado, selecione **Connect at power-on**. Em seguida, selecione **Next** > **Finish** > **OK**.

## <a name="register-the-configuration-server-with-azure-site-recovery-services"></a>Registe o servidor de configuração com serviços de recuperação do site Azure

1. A partir da consola do VMWare vSphere Client, ligue a VM.
2. A VM arranca para uma experiência de instalação do Windows Server 2016. Aceite o contrato de licença e introduza uma palavra-passe de administrador.
3. Após a conclusão da instalação, inicie sessão na VM como administrador.
4. A primeira vez que inicia a sua inscrição, em poucos segundos começa a ferramenta de configuração de configuração de recuperação do local Azure.
5. Introduza um nome que será utilizado para registar o servidor de configuração no Site Recovery. Em seguida, selecione **Seguinte**.
6. A ferramenta verifica se a VM pode ligar ao Azure. Depois de a ligação estar estabelecida, selecione **Iniciar sessão** para iniciar sessão na sua subscrição do Azure.</br>
    a. As credenciais têm de ter acesso ao cofre no qual pretende registar o servidor de configuração.</br>
    b. Certifique-se de que a conta de utilizador escolhida tem permissão para criar uma aplicação no Azure. Para permitir as permissões necessárias, siga as diretrizes nos requisitos de permissão de [diretório ativo azure](#azure-active-directory-permission-requirements).
7. A ferramenta executa algumas tarefas de configuração e, em seguida, é reiniciada.
8. Inicie sessão na máquina novamente. O assistente de gestão do servidor de configuração começa automaticamente em poucos segundos.

### <a name="configure-settings"></a>Configurar definições

1. No assistente de gestão do servidor de configuração, **selecione conectividade Configuração**. A partir das caixas de entrega, primeiro selecione o NIC que o servidor de processo incorporado utiliza para a descoberta e impulsionar a instalação do serviço de mobilidade em máquinas de origem. Em seguida, selecione o NIC que o servidor de configuração utiliza para a conectividade com o Azure. Selecione **Guardar**. Não é possível alterar esta definição depois de estar configurada. Não altere o endereço IP de um servidor de configuração. Certifique-se de que o IP atribuído ao servidor de configuração é um IP estático e não um IP DHCP.
2. No **cofre Select Recovery Services,** inscreva-se no Microsoft Azure com as credenciais utilizadas no passo 6 do Registo do servidor de [configuração com serviços](#register-the-configuration-server-with-azure-site-recovery-services)de recuperação do site Azure .
3. Após o início de sessão, selecione a sua subscrição Azure e o grupo de recursos e cofre relevantes.

    > [!NOTE]
    > Após o registo, não há flexibilidade para mudar o cofre dos serviços de recuperação.
    > A alteração de um cofre de serviços de recuperação requer a desassociação do servidor de configuração a partir do cofre atual, e a replicação de todas as máquinas virtuais protegidas sob o servidor de configuração é interrompida. Para saber mais, consulte [Gerir o servidor de configuração para recuperação de desastres VMware VM](vmware-azure-manage-configuration-server.md#register-a-configuration-server-with-a-different-vault).

4. Em **instalar software de terceiros:**

    |Cenário   |Passos a seguir  |
    |---------|---------|
    |Posso descarregar e instalar o MySQL manualmente?     |  Sim. Descarregue a aplicação MySQL, coloque-a na pasta **C:\Temp\ASRSetup**, e depois instale manualmente. Depois de aceitar os termos e selecionar **Descarregar e instalar,** o portal diz *já instalado*. Pode passar ao próximo passo.       |
    |Posso evitar o download do MySQL online?     |   Sim. Coloque a sua aplicação de instalação MySQL na pasta **C:\Temp\ASRSetup**. Aceite os termos, selecione **Descarregar e instalar,** e o portal utiliza o instalador que adicionou para instalar a aplicação. Após a instalação terminar, proceda ao passo seguinte.    |
    |Quero descarregar e instalar o MySQL através da Recuperação do Site Azure.    |  Aceite o contrato de licença e selecione **Descarregar e instalar**. Após a instalação terminar, proceda ao passo seguinte.       |

5. Na configuração do **aparelho De validação,** verificam-se pré-requisitos antes de continuar.
6. No **servidor Configure vCenter Server/vSphere ESXi,** introduza o endereço FQDN ou IP do servidor vCenter ou anfitrião vSphere, onde estão localizados os VMs que pretende replicar. Introduza a porta em que o servidor está a escutar. Introduza um nome amigável a utilizar para o servidor VMware no cofre.
7. Introduza as credenciais que o servidor de configuração irá utilizar para ligar ao servidor VMware. O Site Recovery utiliza estas credenciais para detetar automaticamente as VMs VMware que estão disponíveis para replicação. **Selecione Adicionar** > **Continuar**. As credenciais aqui introduzidas são guardadas localmente.
8. No **Configure credenciais de máquina virtual,** introduza o nome de utilizador e a palavra-passe das máquinas virtuais para instalar automaticamente o serviço de mobilidade durante a replicação. Para as máquinas **Windows,** a conta necessita de privilégios de administrador local nas máquinas que pretende replicar. Para **linux,** forneça detalhes para a conta raiz.
9. Selecione **Finalizar configuração** para concluir o registo.
10. Após o registo terminar, abra o portal Azure e verifique se o servidor de configuração e vMware estão listados no Cofre de Serviços de **Recuperação** > **Gerir** > Infraestrutura de **Recuperação** do Site > Servidores de **Configuração**.

## <a name="upgrade-the-configuration-server"></a>Atualizar o servidor de configuração

Para atualizar o servidor de configuração para a versão mais recente, consulte [Gerir o servidor de configuração para recuperação de desastres VMware VM](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server). Para obter instruções sobre como atualizar todos os componentes de Recuperação do Site, consulte [as atualizações do Serviço na Recuperação do Site](service-updates-how-to.md).

## <a name="manage-the-configuration-server"></a>Gerir o servidor de configuração

Para evitar interrupções na replicação em curso, certifique-se de que o endereço IP do servidor de configuração não se altera depois de o servidor de configuração estar registado num cofre. Para saber mais sobre as tarefas comuns de gestão do servidor de configuração, consulte [Gerir o servidor de configuração para recuperação de desastres VMware VM](vmware-azure-manage-configuration-server.md).

## <a name="troubleshoot-deployment-issues"></a>Resolver problemas de implementação

Consulte o nosso artigo de resolução de [problemas](vmware-azure-troubleshoot-configuration-server.md) para resolver problemas de implementação e conectividade.

## <a name="faqs"></a>FAQs

* Quanto tempo é que a licença é fornecida num servidor de configuração implementado através do OVF válido? O que acontece se eu não reativar a licença?

    A licença fornecida com um modelo OVA é uma licença de avaliação válida por 180 dias. Antes de expirar, precisa ativar a licença. Caso contrário, pode resultar num encerramento frequente do servidor de configuração e causar um obstáculo às atividades de replicação. Para mais informações, consulte [Gerir o servidor de configuração para recuperação de desastres VMware VM](vmware-azure-manage-configuration-server.md#update-windows-license).

* Posso usar o VM onde o servidor de configuração é instalado para diferentes fins?

    Não. Utilize o VM para o único fim do servidor de configuração. Certifique-se de que segue todas as especificações mencionadas nos [pré-requisitos](#prerequisites) para uma gestão eficiente da recuperação de desastres.
* Posso trocar o cofre já registado no servidor de configuração com um cofre recém-criado?

    Não. Depois de um cofre estar registado no servidor de configuração, não pode ser alterado.
* Posso usar o mesmo servidor de configuração para proteger máquinas físicas e virtuais?

    Sim. O mesmo servidor de configuração pode ser utilizado para replicar máquinas físicas e virtuais. No entanto, a máquina física só pode ser reprovada num VMware VM.
* Qual é o propósito de um servidor de configuração e onde é usado?

    Para saber mais sobre o servidor de configuração e as suas funcionalidades, consulte a arquitetura de [replicação Do VMware para o Azure](vmware-azure-architecture.md).
* Onde posso encontrar a versão mais recente do servidor de configuração?

    Para passos para atualizar o servidor de configuração através do portal, consulte [atualizar o servidor de configuração](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server). Para obter instruções sobre como atualizar todos os componentes de Recuperação do Site, consulte [as atualizações do Serviço na Recuperação do Site](https://aka.ms/asr_how_to_upgrade).
* Onde posso descarregar a frase de passe para o servidor de configuração?

    Para descarregar a frase de passe, consulte [Gerir o servidor de configuração para recuperação de desastres VMware VM](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase).
* Posso mudar a frase de passe?

    Não. Não altere a frase de passe do servidor de configuração. Uma mudança na frase-passe quebra a replicação de máquinas protegidas e leva a um estado de saúde crítico.
* Onde posso baixar as chaves de registo do cofre?

    No **Cofre de Serviços de Recuperação,** selecione **Gerir** > Infraestrutura de **Recuperação** de > Servidores de **Configuração**. Nos **Servidores,** selecione **a chave de registo de descarregamento** para descarregar o ficheiro de credenciais de cofre.
* Posso clonar um servidor de configuração existente e usá-lo para orquestração de replicação?

    Não. A utilização de um componente de servidor de configuração clonado não é suportada. A clonagem de um servidor de processo sem escala salta também é um cenário não suportado. Os componentes de recuperação do site de clonagem afetam as replicações em curso.

* Posso alterar o IP de um servidor de configuração?

    Não. Não altere o endereço IP de um servidor de configuração. Certifique-se de que todos os IPs atribuídos ao servidor de configuração são IPs estáticos e não IPs DHCP.
* Posso configurar um servidor de configuração no Azure?

    Instale um servidor de configuração num ambiente no local com uma linha de visão direta com o V-Center e minimize as tardios de transferência de dados. Pode utilizar cópias de segurança programadas do servidor de configuração para efeitos de [falha](vmware-azure-manage-configuration-server.md#failback-requirements).

* Posso alterar o controlador de cache num servidor de configuração ou num servidor de processo sem escalas?

    Não, o condutor cache não pode ser trocado uma vez que a configuração esteja completa.

Para mais PERGUNTAS nas Perguntas de Configuração, consulte [as questões comuns](vmware-azure-common-questions.md#configuration-server)do servidor de configuração .

## <a name="next-steps"></a>Passos Seguintes

Instale a recuperação de desastres de [VMware VMs](vmware-azure-tutorial.md) para o Azure.
