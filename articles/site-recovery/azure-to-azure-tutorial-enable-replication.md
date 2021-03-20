---
title: Tutorial para criar recuperação de desastres Azure VM com recuperação do local de Azure
description: Neste tutorial, crie a recuperação de desastres para os VMs Azure para outra região do Azure, utilizando o serviço de Recuperação de Sítios.
ms.topic: tutorial
ms.date: 11/03/2020
ms.custom: mvc
ms.openlocfilehash: 473a264ef497cab4bd4f88372600161b33178099
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97656874"
---
# <a name="tutorial-set-up-disaster-recovery-for-azure-vms"></a>Tutorial: Criar recuperação de desastres para VMs Azure

Este tutorial mostra-lhe como configurar a recuperação de desastres para os VMs Azure usando [a Recuperação do Local de Azure.](site-recovery-overview.md) Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Verifique as configurações e permissões do Azure
> * Prepare VMs que pretende replicar
> * Criar um cofre dos Serviços de Recuperação 
> * Ativar a replicação de VM

Quando permite a replicação de um VM para configurar a recuperação de desastres, a extensão do serviço de mobilidade de recuperação do local instala-se no VM e regista-a com a Recuperação do Local de Azure. Durante a replicação, as gravações em disco VM são enviadas para uma conta de armazenamento de cache na região de origem. Os dados são enviados de lá para a região alvo, e os pontos de recuperação são gerados a partir dos dados. Quando falhas num VM durante a recuperação de desastres, é utilizado um ponto de recuperação para restaurar o VM na região alvo.

> [!NOTE]
> Os tutoriais fornecem instruções com as definições predefinitivas mais simples. Se pretender configurar a recuperação de desastres Azure VM com configurações personalizadas, [reveja este artigo](azure-to-azure-how-to-enable-replication.md).

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este tutorial:

- [Rever as regiões apoiadas.](azure-to-azure-support-matrix.md#region-support) Você pode configurar a recuperação de desastres para VMs Azure entre qualquer duas regiões na mesma geografia.
- Precisa de um ou mais VMs Azure. Verifique se [os VMs Windows](azure-to-azure-support-matrix.md#windows) ou [Linux](azure-to-azure-support-matrix.md#replicated-machines---linux-file-systemguest-storage) estão suportados.
- [Reveja os](azure-to-azure-support-matrix.md#replicated-machines---compute-settings)requisitos de cálculo VM, [armazenamento](azure-to-azure-support-matrix.md#replicated-machines---storage)e [networking.](azure-to-azure-support-matrix.md#replicated-machines---networking)
- Este tutorial presume que os VM não são encriptados. Se pretender configurar a recuperação de desastres para VMs encriptados, [siga este artigo](azure-to-azure-how-to-enable-replication-ade-vms.md).

## <a name="check-azure-settings"></a>Verifique as definições do Azure

Verifique permissões e configurações na região alvo.

### <a name="check-permissions"></a>Verificar permissões

A sua conta Azure precisa de permissões para criar um cofre dos Serviços de Recuperação e para criar VMs na região alvo.

- Se acabou de criar uma subscrição gratuita do Azure, é o administrador da conta e não são necessárias mais medidas.
- Se não és o administrador, trabalha com o administrador para obter as permissões de que precisas.
    - **Criar um cofre**: Permissões de administrador ou proprietário na subscrição. 
    - **Gerir as operações de recuperação do local no cofre**: O contributo de *recuperação* do local incorporado Azure.
    - **Criar VMs Azure na região alvo**: Ou o papel *de contribuinte de máquina virtual* incorporado, ou permissões específicas para:
        - Criar uma VM na rede virtual selecionada.
        - Escreva para uma conta de armazenamento Azure.
        - Escreva para um disco gerido pelo Azure.

### <a name="verify-target-settings"></a>Verificar definições de alvo

Durante a recuperação de desastres, quando se falha na região de origem, os VM são criados na região alvo. 

Verifique se a sua subscrição tem recursos suficientes na região alvo. É necessário ser capaz de criar VMs com tamanhos que correspondam aos VMs na região de origem. Quando configurar a recuperação de desastres, a Recuperação do Local escolhe o mesmo tamanho (ou o tamanho mais próximo possível) para o VM alvo.


## <a name="prepare-vms"></a>Preparar VMs

Certifique-se de que os VMs têm conectividade de saída e os certificados de raiz mais recentes. 


### <a name="set-up-vm-connectivity"></a>Configurar conectividade VM

VMs que pretende replicar precisam de conectividade de rede de saída.

> [!NOTE]
> A Recuperação do Site não suporta a utilização de um representante de autenticação para controlar a conectividade da rede.

#### <a name="outbound-connectivity-for-urls"></a>Conectividade de saída para URLs

Se estiver a usar um proxy de firewall baseado em URL para controlar a conectividade de saída, permita o acesso a estes URLs:

| **Nome**                  | **Comercial**                               | **Administração Pública**                                 | **Descrição** |
| ------------------------- | -------------------------------------------- | ---------------------------------------------- | ----------- |
| Armazenamento                   | `*.blob.core.windows.net`                  | `*.blob.core.usgovcloudapi.net` | Permite que os dados sejam escritos da VM para a conta de armazenamento em cache na região de origem. |
| Azure Active Directory    | `login.microsoftonline.com`                | `login.microsoftonline.us`                   | Fornece autorização e autenticação para os URLs do serviço Site Recovery. |
| Replicação               | `*.hypervrecoverymanager.windowsazure.com` | `*.hypervrecoverymanager.windowsazure.com`   | Permite que a VM comunique com o serviço Site Recovery. |
| Service Bus               | `*.servicebus.windows.net`                 | `*.servicebus.usgovcloudapi.net`             | Permite que a VM escreva dados de monitorização e diagnóstico do Site Recovery. |

#### <a name="outbound-connectivity-for-ip-address-ranges"></a>Conectividade de saída para intervalos de endereços IP

Se estiver a utilizar grupos de segurança de rede (NSGs) para controlar a conectividade, crie regras NSG baseadas em etiquetas de serviço que permitam a saída do HTTPS para a porta 443 para estas [tags](../virtual-network/service-tags-overview.md#available-service-tags)de serviço (grupos de endereços IP):

**Tag** | **Permitir** 
--- | ---
Etiqueta de armazenamento  |Permite que os dados sejam escritos do VM para a conta de armazenamento de cache.   
Etiqueta AD Azure | Permite o acesso a todos os endereços IP que correspondam ao Azure AD.   
Tag EventsHub | Permite o acesso à monitorização da Recuperação do Local.  
Etiqueta AzureSiteRecovery | Permite o acesso ao serviço de Recuperação do Local em qualquer região.   
Etiqueta de Gestão de Hóspedes EHybrid | Utilize se pretender atualizar automaticamente o agente de Mobilidade de Recuperação do Site que está a funcionar em VMs ativados para replicação.

[Saiba mais](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags) sobre as etiquetas necessárias e exemplos de marcação.

### <a name="verify-vm-certificates"></a>Verificar os certificados da VM

Verifique se os VMs têm os certificados de raiz mais recentes. Caso contrário, o VM não pode ser registado na Recuperação do Local devido a restrições de segurança.

- **VMs do Windows**: Instale todas as atualizações mais recentes do Windows no VM, de modo a que todos os certificados de raiz fidedignos estejam na máquina. Num ambiente desligado, siga os seus processos padrão para o Windows Update e as atualizações de certificados.
- **Linux VMs**: Siga as orientações fornecidas pelo seu distribuidor Linux, para obter os mais recentes certificados de raiz fidedignos e lista de revogação de certificados (CRL).

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação 

Crie um cofre dos Serviços de Recuperação em qualquer região, exceto na região de origem a partir da qual pretende replicar VMs.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Na caixa de pesquisa, escreva *a recuperação.* Em **Serviços**, selecione **Cofres dos Serviços de Recuperação.**

    ![Pesquisa de cofres dos Serviços de Recuperação](./media/azure-to-azure-tutorial-enable-replication/search.png)

3. Nos **cofres dos Serviços de Recuperação**, selecione **Add**.
4. In **Create Recovery Services vault**  >  **Basics**, selecione a subscrição na qual criar o cofre.
5. No **grupo De recursos,** selecione um grupo de recursos existente para o cofre ou crie um novo.
6. Em **nome do Cofre,** especifique um nome amigável para identificar o cofre.
7. Na **Região,** selecione a região de Azure para colocar o cofre. [Verifique as regiões apoiadas.](https://azure.microsoft.com/pricing/details/site-recovery/)
8. Selecione **Rever + criar**.

   ![Definições de cofre na página para criar um novo cofre](./media/azure-to-azure-tutorial-enable-replication/vault-basics.png)

9. Em **Rever + criar,** selecione **Criar**.

10. A implantação do cofre começa. Acompanhe os progressos nas notificações.
11. Depois de o cofre ser implantado, selecione **Pin para o painel de instrumentos** para guardar para uma referência rápida. Selecione **Vá para o recurso** para abrir o novo cofre. 
    
    ![Botões para abrir o cofre após a implantação, e fixação no painel de instrumentos](./media/azure-to-azure-tutorial-enable-replication/vault-deploy.png)

### <a name="enable-site-recovery"></a>Ativar a recuperação do site

Nas definições do cofre, selecione **Ativar a recuperação do local**.

![Seleção para permitir a recuperação do local no cofre](./media/azure-to-azure-tutorial-enable-replication/enable-site-recovery.png)

## <a name="enable-replication"></a>Ativar a replicação

Selecione as definições de origem e ative a replicação de VM. 

### <a name="select-source-settings"></a>Selecione definições de origem

1. No cofre > página **de Recuperação do Local,** sob **máquinas virtuais Azure**, selecione **Ativar a replicação**.

    ![Seleção para permitir a replicação de VMs Azure](./media/azure-to-azure-tutorial-enable-replication/enable-replication.png)

2. Na **localização Source** >  **Source**, selecione a região de Azure de origem em que os VMs estão atualmente em execução.
3. No **modelo de implantação da máquina virtual Azure,** deixe a definição de **Gestor de Recursos** predefinido.
4. Na **subscrição Source**, selecione a subscrição em que os VMs estão a ser em execução. Você pode selecionar qualquer subscrição que esteja no mesmo inquilino do Azure Ative Directory (AD) que o cofre.
5. No **grupo de recursos Source**, selecione o grupo de recursos que contém os VMs.
6. Na **recuperação de desastres entre zonas de disponibilidade,** deixe a definição padrão **No.**

     ![Configurar a origem](./media/azure-to-azure-tutorial-enable-replication/source.png)

7. Selecione **Seguinte**.

### <a name="select-the-vms"></a>Selecionar as VMs

A Recuperação do Site recupera os VMs associados ao grupo de subscrição/recursos selecionado.

1. Em **Máquinas Virtuais,** selecione os VMs que pretende ativar para a recuperação de desastres.

     ![Página para selecionar VMs para replicação](./media/azure-to-azure-tutorial-enable-replication/select-vm.png)

2. Selecione **Seguinte**.

### <a name="review-replication-settings"></a>Rever definições de replicação

1. Nas **definições de replicação,** reveja as definições. A Recuperação do Site cria definições/políticas predefinidos para a região alvo. Para efeitos deste tutorial, utilizamos as definições predefinidos.
2. Selecione **Ativar replicação**.

    ![Página para personalizar definições e ativar a replicação](./media/azure-to-azure-tutorial-enable-replication/enable-vm-replication.png)   

3. Acompanhe o progresso da replicação nas notificações.

     ![Acompanhe o progresso nas notificações ](./media/azure-to-azure-tutorial-enable-replication/notification.png) ![ Acompanhe a notificação de replicação bem sucedida](./media/azure-to-azure-tutorial-enable-replication/notification-success.png)

4. Os VMs que ativa aparecem na página de **itens replicados** > cofre.

    ![VM na página de Itens Replicados](./media/azure-to-azure-tutorial-enable-replication/replicated-items.png)


## <a name="next-steps"></a>Passos seguintes

Neste tutorial, permitiu a recuperação de desastres para um VM Azure. Agora, faça um exercício para verificar se o fracasso funciona como esperado.

> [!div class="nextstepaction"]
> [Executar um teste de recuperação após desastre](azure-to-azure-tutorial-dr-drill.md)
