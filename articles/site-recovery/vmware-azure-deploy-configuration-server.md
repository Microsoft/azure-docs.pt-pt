---
title: Implantar o servidor de configuração para a recuperação de desastres do VMware com o Azure Site Recovery | Microsoft Docs
description: Este artigo descreve como implantar um servidor de configuração para a recuperação de desastres do VMware com o Azure Site Recovery
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/15/2019
ms.author: ramamill
ms.openlocfilehash: 5812cc73fb1da58c591d0593e079851e05bd0940
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72331964"
---
# <a name="deploy-a-configuration-server"></a>Implementar um servidor de configuração

Você implanta um servidor de configuração local ao usar [Azure site Recovery](site-recovery-overview.md) para recuperação de desastre de VMs VMware e servidores físicos no Azure. O servidor de configuração coordena as comunicações entre o VMware local e o Azure. Ele também gerencia a replicação de dados. Este artigo orienta você pelas etapas necessárias para implantar o servidor de configuração quando você está replicando VMs do VMware para o Azure. [Siga este artigo](physical-azure-set-up-source.md) se você precisar configurar um servidor de configuração para replicação de servidor físico.

> [!TIP]
> Você pode aprender sobre a função do servidor de configuração como parte da arquitetura de Azure Site Recovery [aqui](vmware-azure-architecture.md).

## <a name="deployment-of-configuration-server-through-ova-template"></a>Implantação do servidor de configuração por meio do modelo OVA

O servidor de configuração deve ser configurado como uma VM VMware altamente disponível com determinados requisitos mínimos de hardware e de dimensionamento. Para uma implantação conveniente e fácil, o Site Recovery fornece um modelo OVA para download (aplicativo de virtualização aberta) para configurar o servidor de configuração que está em conformidade com todos os requisitos obrigatórios listados abaixo.

## <a name="prerequisites"></a>Pré-requisitos

Os requisitos mínimos de hardware para um servidor de configuração são resumidos na tabela a seguir.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="azure-active-directory-permission-requirements"></a>Requisitos de permissão do Azure Active Directory

Você precisa de um usuário com **uma das seguintes** permissões definidas no AAD (Azure Active Directory) para registrar o servidor de configuração com os serviços Azure site Recovery.

1. O usuário deve ter a função "desenvolvedor do aplicativo" para criar o aplicativo.
   1. Para verificar, entre no portal do Azure</br>
   1. Navegue até Azure Active Directory funções e administradores do ></br>
   1. Verifique se a função "desenvolvedor de aplicativos" está atribuída ao usuário. Caso contrário, use um usuário com essa permissão ou entre em contato com [o administrador para habilitar a permissão](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal#assign-roles).
    
1. Se a função "desenvolvedor de aplicativos" não puder ser atribuída, verifique se o sinalizador "o usuário pode registrar o aplicativo" está definido como verdadeiro para o usuário criar a identidade. Para habilitar as permissões acima,
   1. Iniciar sessão no portal do Azure
   1. Navegue até Azure Active Directory > configurações de usuário
   1. Em * * Registros de aplicativo "," os usuários podem registrar aplicativos "devem ser escolhidos como" Sim ".

      ![AAD_application_permission](media/vmware-azure-deploy-configuration-server/AAD_application_permission.png)

> [!NOTE]
> **Não há suporte para**serviços de Federação do Active Directory (AD FS) (ADFS). Use uma conta gerenciada por meio de [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis).

## <a name="capacity-planning"></a>Planeamento de capacidade

Os requisitos de dimensionamento para o servidor de configuração dependem da taxa potencial de alteração de dados. Use esta tabela como um guia.

| **CPUs** | **Memória** | **Tamanho do disco de cache** | **Taxa de alteração de dados** | **Máquinas protegidas** |
| --- | --- | --- | --- | --- |
| 8 vCPUs (2 soquetes * 4 núcleos \@ 2,5 GHz) |16 GB |300 GB |500 GB ou menos |Replicar menos de 100 computadores. |
| 12 vCPUs (2 soquetes * 6 núcleos \@ 2,5 GHz) |18 GB |600 GB |500 GB a 1 TB |Replique 100-150 máquinas. |
| 16 vCPUs (2 soquetes * 8 núcleos \@ 2,5 GHz) |32 GB |1 TB |1 TB a 2 TB |Replique 150-200 máquinas. |

Se você estiver replicando mais de uma VM do VMware, leia [Considerações sobre planejamento de capacidade](site-recovery-plan-capacity-vmware.md). Execute a [ferramenta planejador de implantação](site-recovery-deployment-planner.md) para replicação do VMware.

## <a name="download-the-template"></a>Transferir o modelo

1. No cofre, vá para **Preparar Infraestrutura** > **Origem**.
2. Em **Preparar origem**, selecione **+Servidor de configuração**.
3. Em **Adicionar Servidor**, verifique se o **Servidor de configuração para VMware** é apresentado no **Tipo de servidor**.
4. Baixe o modelo OVA (Open Virtualization Application) para o servidor de configuração.

   > [!TIP]
   >Você também pode baixar a versão mais recente do modelo do servidor de configuração diretamente do [centro de download da Microsoft](https://aka.ms/asrconfigurationserver).

> [!NOTE]
> A licença fornecida com o modelo OVA é uma licença de avaliação válida por 180 dias. Poste esse período, o cliente precisa ativar as janelas com uma licença adquirida.

## <a name="import-the-template-in-vmware"></a>Importar o modelo no VMware

1. Inicie sessão no servidor VMware vCenter ou no anfitrião vSphere ESXi, utilizando o VMWare vSphere Client.
2. No menu **Ficheiro**, selecione **Implementar o Modelo OVF**, para iniciar o assistente para Implementar o Modelo OVF.

     ![Modelo OVF](./media/vmware-azure-deploy-configuration-server/vcenter-wizard.png)

3. Em **selecionar origem**, insira o local do OVF baixado.
4. Em **detalhes da revisão**, selecione **Avançar**.
5. Em **selecionar nome e pasta** e **selecione configuração**, aceite as configurações padrão.
6. Em **Selecionar armazenamento**, para obter o melhor desempenho, selecione **Aprovisionamento Intenso Diligente Posto a Zero** em **Selecionar formato de disco virtual**. O uso da opção de provisionamento dinâmico pode afetar o desempenho do servidor de configuração.
7. No resto das páginas do assistente, aceite as predefinições.
8. Em **Pronto para concluir**:

    * Para configurar a VM com as predefinições, selecione **Ligar após a implementação** > **Concluir**.

    * Para adicionar uma interface de rede adicional, desmarque **ligar após a implantação**e, em seguida, selecione **concluir**. Por predefinição, o modelo do servidor de configuração é implementado com um único NIC. Pode adicionar mais NICs após a implementação.

> [!IMPORTANT]
> Não altere as configurações de recursos (restrição de memória/núcleos/CPU), modifique/exclua os serviços ou arquivos instalados no servidor de configuração após a implantação. Isso afetará o registro do servidor de configuração com os serviços do Azure e o desempenho do servidor de configuração.

## <a name="add-an-additional-adapter"></a>Adicionar outro adaptador

> [!NOTE]
> Duas NICs serão necessárias se você estiver planejando manter os endereços IP dos computadores de origem no failover e desejar fazer failback para o local mais tarde. Uma NIC será conectada a computadores de origem e a outra NIC será usada para a conectividade do Azure.

Se você quiser adicionar uma NIC adicional ao servidor de configuração, adicione-a antes de registrar o servidor no cofre. Adicionar mais adaptadores não é suportado após o registo.

1. No inventário do vSphere Client, clique na VM com o botão direito do rato e selecione **Editar Definições**.
2. Em **Hardware**, selecione **Adicionar** > **Adaptador Ethernet**. Em seguida, selecione **Seguinte**.
3. Selecione um tipo de adaptador e uma rede.
4. Para conectar a NIC virtual quando a VM estiver ativada, selecione **conectar no Power-on**. Em seguida, selecione **avançar** > **concluir** > **OK**.

## <a name="register-the-configuration-server-with-azure-site-recovery-services"></a>Registrar o servidor de configuração com os serviços Azure Site Recoverys

1. A partir da consola do VMWare vSphere Client, ligue a VM.
2. A VM arranca para uma experiência de instalação do Windows Server 2016. Aceite o contrato de licença e introduza uma palavra-passe de administrador.
3. Após a conclusão da instalação, inicie sessão na VM como administrador.
4. Na primeira vez que você entrar, em poucos segundos a ferramenta de configuração do Azure Site Recovery será iniciada.
5. Introduza um nome que será utilizado para registar o servidor de configuração no Site Recovery. Em seguida, selecione **Seguinte**.
6. A ferramenta verifica se a VM pode ligar ao Azure. Depois de a ligação estar estabelecida, selecione **Iniciar sessão** para iniciar sessão na sua subscrição do Azure.</br>
    a. As credenciais têm de ter acesso ao cofre no qual pretende registar o servidor de configuração.</br>
    b. Verifique se a conta de usuário escolhida tem permissões para criar um aplicativo no Azure. Para habilitar as permissões necessárias, siga as diretrizes fornecidas [aqui](#azure-active-directory-permission-requirements).
7. A ferramenta executa algumas tarefas de configuração e, em seguida, é reiniciada.
8. Inicie sessão na máquina novamente. O assistente de gerenciamento do servidor de configuração é iniciado **automaticamente** em alguns segundos.

### <a name="configure-settings"></a>Configurar definições

1. No assistente de gerenciamento do servidor de configuração, selecione **instalação conectividade**. Nos menus suspensos, primeiro selecione a NIC que o servidor de processo interno usa para descoberta e instalação por push do serviço de mobilidade em computadores de origem e selecione a NIC que o servidor de configuração usa para conectividade com o Azure. Em seguida, selecione **Guardar**. Você não pode alterar essa configuração depois que ela é configurada. É altamente recomendável não alterar o endereço IP de um servidor de configuração. Verifique se o IP atribuído ao servidor de configuração é IP estático e não IP do DHCP.
2. Em **selecionar cofre dos serviços de recuperação**, entre no Microsoft Azure com as credenciais usadas na **etapa 6** de "[registrar servidor de configuração com serviços de Azure site Recovery](#register-the-configuration-server-with-azure-site-recovery-services)".
3. Depois de entrar, selecione sua assinatura do Azure e o grupo de recursos e o cofre relevantes.

    > [!NOTE]
    > Depois de registrado, não há flexibilidade para alterar o cofre dos serviços de recuperação.
    > A alteração do cofre de serviços de recuperação exigiria a desassociação do servidor de configuração do cofre atual, e a replicação de todas as máquinas virtuais protegidas no servidor de configuração será interrompida. Saiba [mais](vmware-azure-manage-configuration-server.md#register-a-configuration-server-with-a-different-vault).

4. Em **instalar software de**terceiros,

    |Cenário   |Etapas a seguir  |
    |---------|---------|
    |Posso baixar & instalar o MySQL manualmente?     |  Sim. Baixe o aplicativo MySQL & Coloque-o na pasta **C:\Temp\ASRSetup**e instale manualmente. Agora, quando você aceita os termos > clicar em **baixar e instalar**, o portal diz *já instalado*. Você pode prosseguir para a próxima etapa.       |
    |Posso evitar o download do MySQL online?     |   Sim. Coloque seu aplicativo de instalador do MySQL na pasta **C:\Temp\ASRSetup**. Aceite os termos > clique em **baixar e instalar**, o portal usará o instalador adicionado por você e instalará o aplicativo. Você pode prosseguir para a próxima etapa pós-instalação.    |
    |Eu gostaria de baixar & instalar o MySQL por meio de Azure Site Recovery     |  Aceite o contrato de licença & clique em **baixar e instalar**. Em seguida, você pode prosseguir para a próxima etapa após a instalação.       |

5. Em **Validar configuração da aplicação**, os pré-requisitos são verificados antes de continuar.
6. Em **Configurar servidor vCenter Server/vSphere ESXi**, introduza o FQDN ou endereço IP do servidor vCenter ou anfitrião vSphere onde estão localizadas as VMs que pretende replicar. Introduza a porta em que o servidor está a escutar. Introduza um nome amigável a utilizar para o servidor VMware no cofre.
7. Introduza as credenciais que o servidor de configuração irá utilizar para ligar ao servidor VMware. O Site Recovery utiliza estas credenciais para detetar automaticamente as VMs VMware que estão disponíveis para replicação. Selecione **Adicionar**e **continuar**. As credenciais inseridas aqui são salvas localmente.
8. Em **configurar credenciais da máquina virtual**, insira o nome de usuário e a senha das máquinas virtuais para instalar automaticamente o serviço de mobilidade durante a replicação. Para computadores **Windows** , a conta precisa de privilégios de administrador local nos computadores que você deseja replicar. Para o **Linux**, forneça detalhes para a conta raiz.
9. Selecione **Finalizar configuração** para concluir o registo.
10. Após a conclusão do registro, abra portal do Azure, verifique se o servidor de configuração e o servidor VMware estão listados no **cofre dos serviços de recuperação** > **gerenciar** > **site Recovery**configuração de infraestrutura  >  **Servidores**.

## <a name="upgrade-the-configuration-server"></a>Atualizar o servidor de configuração

Para atualizar o servidor de configuração para a versão mais recente, siga estas [etapas](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server). Para obter instruções detalhadas sobre como atualizar todos os componentes do Site Recovery, clique [aqui](service-updates-how-to.md).

## <a name="manage-the-configuration-server"></a>Gerir o servidor de configuração

Para evitar interrupções na replicação em andamento, certifique-se de que o endereço IP do servidor de configuração não seja alterado depois que o servidor de configuração tiver sido registrado em um cofre. Você pode aprender mais sobre as tarefas comuns de gerenciamento do servidor de configuração [aqui](vmware-azure-manage-configuration-server.md).

## <a name="faq"></a>FAQ

1. Por quanto tempo a licença fornecida no servidor de configuração implantada por meio de OVF é válida? O que acontece se eu não reativar a licença?

    A licença fornecida com o modelo OVA é uma licença de avaliação válida por 180 dias. Antes da expiração, você precisa ativar a licença. Ou seja, isso pode resultar em um desligamento frequente do servidor de configuração e, portanto, causar a impedimento das atividades de replicação.

2. Posso usar a VM, onde o servidor de configuração está instalado, para finalidades diferentes?

    **Não**, recomendamos que você use a VM para fins de uso único do servidor de configuração. Certifique-se de seguir todas as especificações mencionadas em [pré-requisitos](#prerequisites) para o gerenciamento eficiente da recuperação de desastres.
3. Posso mudar o cofre já registrado no servidor de configuração com um cofre recém-criado?

    **Não**, depois que um cofre é registrado no servidor de configuração, ele não pode ser alterado.
4. Posso usar o mesmo servidor de configuração para proteger máquinas físicas e virtuais?

    **Sim**, o mesmo servidor de configuração pode ser usado para replicar máquinas físicas e virtuais. No entanto, o computador físico pode passar por failback apenas para uma VM VMware.
5. Qual é a finalidade de um servidor de configuração e onde ele é usado?

    Consulte a [arquitetura de replicação do VMware para o Azure](vmware-azure-architecture.md) para saber mais sobre o servidor de configuração e suas funcionalidades.
6. Onde posso encontrar a versão mais recente do servidor de configuração?

    Para obter as etapas para atualizar o servidor de configuração por meio do portal, consulte [atualizar o servidor de configuração](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server). Para obter instruções detalhadas sobre como atualizar todos os componentes do Site Recovery, consulte [aqui](https://aka.ms/asr_how_to_upgrade).
7. Onde posso baixar a senha do servidor de configuração?

    Consulte [Este artigo](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase) para baixar a frase secreta.
8. Posso alterar a frase secreta?

    **Não**, é **altamente recomendável não alterar a senha** do servidor de configuração. Alteração na senha interrompe a replicação de computadores protegidos e leva a um estado de integridade crítico.
9. Onde posso baixar chaves de registro do cofre?

    No **cofre dos serviços de recuperação**, **gerencie**os**servidores de configuração** > **site Recovery infraestrutura** > . Em servidores, selecione **baixar chave de registro** para baixar o arquivo de credenciais do cofre.
10. Posso clonar um servidor de configuração existente e usá-lo para a orquestração de replicação?

    **Não**, não há suporte para o uso de um componente de servidor de configuração clonado. O clone do servidor de processo de escalabilidade horizontal também é um cenário sem suporte. Clonar Site Recovery componentes impactar as replicações em andamento.

11. Posso alterar o IP do servidor de configuração?

    **Não**, é altamente recomendável não alterar o endereço IP de um servidor de configuração. Verifique se todos os IPs atribuídos ao servidor de configuração são IPs ESTÁTICOs e não IPs de DHCP.
12. Posso configurar o servidor de configuração no Azure?

    É recomendável configurar o servidor de configuração no ambiente local com a linha de visão direta com o v-Center e minimizar as latências de transferência de dados. Você pode fazer backups agendados do servidor de configuração para [fins de failback](vmware-azure-manage-configuration-server.md#failback-requirements).

Para obter mais perguntas frequentes sobre o servidor de configuração, consulte nossa [documentação sobre as perguntas comuns do servidor de configuração](vmware-azure-common-questions.md#configuration-server) .

## <a name="troubleshoot-deployment-issues"></a>Resolver problemas de implementação

[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]



## <a name="next-steps"></a>Passos seguintes

Configure a recuperação de desastre de [VMs do VMware](vmware-azure-tutorial.md) no Azure.
