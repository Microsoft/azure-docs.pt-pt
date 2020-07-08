---
title: Atualização automática do serviço de Mobilidade na Recuperação do Sítio Azure
description: Visão geral da atualização automática do serviço mobility ao replicar VMs Azure utilizando a Recuperação do Local de Azure.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/02/2020
ms.author: rajanaki
ms.openlocfilehash: b2f5faea3df695500ea245d1dc71cb96a84c3643
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/06/2020
ms.locfileid: "85985606"
---
# <a name="automatic-update-of-the-mobility-service-in-azure-to-azure-replication"></a>Atualização automática do serviço de mobilidade na replicação Azure-to-Azure

A Azure Site Recovery utiliza uma cadência de lançamento mensal para corrigir quaisquer problemas e melhorar as funcionalidades existentes ou adicionar novas. Para se manter atual com o serviço, tem de planear a implantação de remendos todos os meses. Para evitar a sobrecarga associada a cada atualização, pode permitir a Recuperação do Site para gerir atualizações de componentes.

Como mencionado na [arquitetura de recuperação de desastres Azure-to-Azure,](azure-to-azure-architecture.md)o serviço de Mobilidade é instalado em todas as máquinas virtuais Azure (VMs) que têm replicação ativada de uma região de Azure para outra. Quando utiliza atualizações automáticas, cada novo lançamento atualiza a extensão do serviço Mobility.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="how-automatic-updates-work"></a>Como funcionam as atualizações automáticas

Quando utiliza a Recuperação do Site para gerir atualizações, implementa um runbook global (utilizado pelos serviços Azure) através de uma conta de automação, criada na mesma subscrição que o cofre. Cada cofre usa uma conta de automação. Para cada VM num cofre, o livro verifica as atualizações automáticas ativas. Se estiver disponível uma versão mais recente da extensão do serviço Mobility, a atualização será instalada.

O horário padrão ocorre diariamente às 00:00 no fuso horário da geografia do VM replicado. Também pode alterar o horário do livro através da conta de automação.

> [!NOTE]
> A partir do [Update Rollup 35,](site-recovery-whats-new.md#updates-march-2019)pode escolher uma conta de automatização existente para utilizar para atualizações. Antes de atualizar o Rollup 35, a Recuperação do Site criou a conta de automação por padrão. Só pode selecionar esta opção quando ativar a replicação de um VM. Não está disponível para um VM que já tenha replicação ativada. A definição selecionada aplica-se a todos os VMs Azure protegidos no mesmo cofre.

Ligar atualizações automáticas não requer um reinício dos seus VMs Azure ou afetar a replicação em curso.

A faturação de emprego na conta de automação baseia-se no número de minutos de trabalho utilizados num mês. A execução de emprego leva alguns segundos a cerca de um minuto por dia e é coberta como unidades livres. Por padrão, 500 minutos são incluídos como unidades gratuitas para uma conta de automação, como mostra o seguinte quadro:

| Unidades gratuitas incluídas (todos os meses) | Preço |
|---|---|
| Tempo de execução de trabalho 500 minutos | ₹0.14/minuto

## <a name="enable-automatic-updates"></a>Ativar atualizações automáticas

Existem várias formas de a Recuperação do Site gerir as atualizações de extensão:

- [Gerir como parte do passo de replicação de ativação](#manage-as-part-of-the-enable-replication-step)
- [Alternar as definições de atualização de extensão dentro do cofre](#toggle-the-extension-update-settings-inside-the-vault)
- [Gerir atualizações manualmente](#manage-updates-manually)

### <a name="manage-as-part-of-the-enable-replication-step"></a>Gerir como parte do passo de replicação de ativação

Quando ativa a replicação de um VM a [partir da vista VM](azure-to-azure-quickstart.md) ou [do cofre dos serviços de recuperação,](azure-to-azure-how-to-enable-replication.md)pode permitir que a Recuperação do Local gere atualizações para a extensão de Recuperação do Site ou a gere manualmente.

:::image type="content" source="./media/azure-to-azure-autoupdate/enable-rep.png" alt-text="Definições de extensão":::

### <a name="toggle-the-extension-update-settings-inside-the-vault"></a>Alternar as definições de atualização de extensão dentro do cofre

1. A partir do cofre dos Serviços de Recuperação, vá para **a**  >  **Infraestrutura de Recuperação do Local.**
1. Em **Baixo para Azure Configurações de**atualização de extensão de máquinas virtuais permitem a  >  **Extension Update Settings**  >  **recuperação do site para gerir**, selecione **On**.

   Para gerir a extensão manualmente, selecione **Off**.

1. Selecione **Guardar**.

:::image type="content" source="./media/azure-to-azure-autoupdate/vault-toggle.png" alt-text="Definições de atualização de extensão":::

> [!IMPORTANT]
> Ao escolher **Permitir a recuperação do local para gerir,** a definição é aplicada a todos os VMs no cofre.

> [!NOTE]
> Qualquer uma das opções notifica-o da conta de automatização utilizada para gerir atualizações. Se estiver a utilizar esta funcionalidade num cofre pela primeira vez, uma nova conta de automação é criada por padrão. Alternadamente, pode personalizar a configuração e escolher uma conta de automação existente. Todas as tomadas subsequentes para permitir a replicação no mesmo cofre utilizarão a conta de automação previamente criada. Atualmente, o menu suspenso apenas listará contas de automação que estão no mesmo Grupo de Recursos que o cofre.

### <a name="manage-updates-manually"></a>Gerir atualizações manualmente

1. Se houver novas atualizações para o serviço mobility instalado nos seus VMs, verá a seguinte notificação: A nova atualização do **agente de replicação do site está disponível. Clique para instalar.**

   :::image type="content" source="./media/vmware-azure-install-mobility-service/replicated-item-notif.png" alt-text="Janela de itens replicados":::

1. Selecione a notificação para abrir a página de seleção VM.
1. Escolha os VMs que pretende atualizar e, em seguida, selecione **OK**. O serviço de Mobilidade de Atualização começará para cada VM selecionado.

   :::image type="content" source="./media/vmware-azure-install-mobility-service/update-okpng.png" alt-text="Lista de VM de itens replicados":::

## <a name="common-issues-and-troubleshooting"></a>Questões comuns e resolução de problemas

Se houver algum problema com as atualizações automáticas, verá uma notificação de erro sob problemas de Configuração no painel de **instrumentos** do cofre.

Se não conseguir ativar atualizações automáticas, consulte os seguintes erros comuns e ações recomendadas:

- **Erro**: Não tem permissões para criar uma conta Azure Run As (principal serviço) e conceder o papel de Contribuinte ao titular do serviço.

  **Ação recomendada**: Certifique-se de que a conta de assinatura é atribuída como Contribuinte e tente novamente. Para obter mais informações sobre a atribuição de permissões, consulte a secção de permissões necessárias de [Como: Utilizar o portal para criar uma aplicação AD AD Azure que possa aceder aos recursos.](/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions)

  Para corrigir a maioria dos problemas depois de ativar atualizações automáticas, selecione **Reparação**. Se o botão de reparação não estiver disponível, consulte a mensagem de erro exibida no painel de definições de atualização de extensão.

  :::image type="content" source="./media/azure-to-azure-autoupdate/repair.png" alt-text="Botão de reparação de serviço de recuperação de local nas definições de atualização de extensão":::

- **Erro**: A conta Run As não tem a permissão para aceder ao recurso de serviços de recuperação.

  **Ação recomendada**: Eliminar e, em seguida, [recriar a conta Run As](/azure/automation/automation-create-runas-account). Ou, certifique-se de que a aplicação Azure Ative Directy da conta Azure Ative da conta pode aceder ao recurso dos serviços de recuperação.

- **Erro**: Executar Como a conta não é encontrada. Qualquer uma delas foi eliminada ou não foi criada - Aplicação de Diretório Ativo Azure, Diretor de Serviço, Papel, Certificado de Automação, Ativo de Ligação de Automação - ou a impressão digital não é idêntica entre Certificado e Conexão.

  **Ação recomendada**: Eliminar e, em seguida, [recriar a conta Run As](/azure/automation/automation-create-runas-account).

- **Erro**: O Azure Run como Certificado utilizado pela conta de automação está prestes a expirar.

  O certificado auto-assinado que é criado para a conta Run As expira um ano a contar da data de criação. Pode renová-lo em qualquer altura antes de expirar. Se se inscreveu para notificações por e-mail, também receberá e-mails quando for necessária uma ação do seu lado. Este erro será mostrado dois meses antes da data de validade e será alterado para um erro crítico se o certificado tiver expirado. Uma vez expirado o certificado, a atualização automática não estará funcional até que renove o mesmo.

  **Ação recomendada**: Para resolver este problema, selecione **Reparar** e, em seguida, Renovar **o Certificado**.

  :::image type="content" source="./media/azure-to-azure-autoupdate/automation-account-renew-runas-certificate.PNG" alt-text="renovar-cert":::

  > [!NOTE]
  > Depois de renovar o certificado, refresque a página para mostrar o estado atual.
