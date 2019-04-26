---
title: Configurar a recuperação de desastre do VMware para o Azure num ambiente de vários inquilinos com o Site Recovery e o programa de fornecedor de soluções Cloud (CSP) | Documentos da Microsoft
description: Descreve como configurar a recuperação de desastre do VMware num ambiente multi-inquilino com o Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: mayg
ms.openlocfilehash: 77b64f09b7fd1429eb23c4407c729dfc0aafdf2b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60461030"
---
# <a name="set-up-vmware-disaster-recovery-in-a-multi-tenancy-environment-with-the-cloud-solution-provider-csp-program"></a>Configurar a recuperação de desastre do VMware num ambiente de vários inquilinos com o programa de fornecedor de soluções Cloud (CSP)

O [programa CSP](https://partner.microsoft.com/en-US/cloud-solution-provider) favorece histórias melhor para serviços cloud da Microsoft, incluindo o Office 365, o Enterprise Mobility Suite e o Microsoft Azure. Com o CSP, os parceiros proprietário, a relação de ponto a ponto com os clientes e tornar-se o ponto de contacto principal da relação. Parceiros podem implementar as subscrições do Azure para clientes e combinar as subscrições com seus próprios ofertas de valor agregadas e personalizadas.

Com o [do Azure Site Recovery](site-recovery-overview.md), como parceiros pode gerir a recuperação após desastre para os clientes diretamente através do CSP. Em alternativa, pode utilizar o CSP para configurar ambientes de recuperação de sites e permitir que os clientes a gerir as suas próprias necessidades de recuperação após desastre de forma self-service. Em ambos os cenários, os parceiros são o elo entre o Site Recovery e os seus clientes. Parceiros de relacionamento com o cliente do serviço e cobrar aos clientes para a utilização do Site Recovery.

Este artigo descreve como, como um parceiro pode criar e gerir subscrições de inquilino através do CSP, para um cenário de replicação de VMware de multi-inquilino.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a replicação de VMware, terá de fazer o seguinte:

- [Preparar](tutorial-prepare-azure.md) recursos do Azure, incluindo uma subscrição do Azure, uma rede virtual do Azure e uma conta de armazenamento.
- [Preparar](vmware-azure-tutorial-prepare-on-premises.md) servidores VMware e VMs no local.
- Para cada inquilino, crie um servidor de gestão separados que pode se comunicar com as VMs do inquilino e os servidores vCenter. Apenas como um parceiro deve ter direitos de acesso a este servidor de gestão. Saiba mais sobre [ambientes de multi-inquilinos](vmware-azure-multi-tenant-overview.md).

## <a name="create-a-tenant-account"></a>Criar uma conta de inquilino

1. Por meio [Microsoft Partner Center](https://partnercenter.microsoft.com/), inicie sessão na sua conta CSP.
2. Sobre o **Dashboard** menu, selecione **clientes**.
3. Na página que se abre, clique nas **Add customer** botão.
4. Na **novo cliente** página, preencha os detalhes de informações de conta para o inquilino.

    ![A página de informações de conta](./media/vmware-azure-multi-tenant-csp-disaster-recovery/customer-add-filled.png)

5. Em seguida, clique em **seguinte: Subscrições**.
6. Na página de seleção de subscrições, selecione **Microsoft Azure** caixa de verificação. Pode adicionar outras assinaturas agora ou em qualquer outra altura.
7. Sobre o **revisão** página, confirme os detalhes de inquilino e, em seguida, clique em **submeter**.
8. Depois de criar a conta de inquilino, é apresentada uma página de confirmação, exibindo os detalhes da conta padrão e a palavra-passe para essa subscrição. Guarde as informações e alterar a palavra-passe mais tarde, se necessário, através do portal do Azure início de sessão na página.

Pode partilhar estas informações com o inquilino como está, ou pode criar e partilhar uma conta separada, se necessário.

## <a name="access-the-tenant-account"></a>Aceder à conta de inquilino

Pode acessar a subscrição do inquilino através do Dashboard do Centro de parceiros Microsoft.

1. Sobre o **clientes** página, clique no nome da conta de inquilino.
2. Na **subscrições** página da conta de inquilino, pode monitorizar as subscrições de conta existente e adicionar mais subscrições, conforme necessário.
3. Para gerir operações de recuperação após desastre do inquilino, selecione **todos os recursos (portal do Azure)**. Isso lhe concede que acesso a subscrições do Azure do inquilino.

    ![A ligação de todos os recursos](./media/vmware-azure-multi-tenant-csp-disaster-recovery/all-resources-select.png)  

4. Pode verificar o acesso ao clicar na ligação do Azure Active Directory no canto superior direito do portal do Azure.

    ![Ligação do Azure Active Directory](./media/vmware-azure-multi-tenant-csp-disaster-recovery/aad-admin-display.png)

Agora pode executar e gerir todas as operações de recuperação de Site para o inquilino no portal do Azure. Aceda à subscrição de inquilino através do CSP para recuperação após desastre gerida, siga o processo descrito anteriormente.

## <a name="assign-tenant-access-to-the-subscription"></a>Atribuir acesso de inquilino para a subscrição

1. Certifique-se de que está configurada a infraestrutura de recuperação após desastre. Parceiros de acedem a subscrições de inquilino através do portal CSP, independentemente se a recuperação após desastre é gerenciada ou Self-Service. Configurar o Cofre e registe-se a infraestrutura para as subscrições de inquilino.
2. Fornecer o inquilino com o [conta que criou](#create-a-tenant-account).
3. Pode adicionar um novo utilizador para a subscrição do inquilino através do portal do CSP da seguinte forma:

    a) aceda à página de subscrição do CSP do inquilino e, em seguida, selecione o **utilizadores e licenças** opção.

      ![Página de subscrição do CSP do inquilino](./media/vmware-azure-multi-tenant-csp-disaster-recovery/users-and-licences.png)

    b) agora crie um novo utilizador ao introduzir os detalhes relevantes e selecionar permissões ou ao carregar a lista de utilizadores num arquivo CSV.
    
    c) depois de criar um novo utilizador, volte ao portal do Azure. Na **subscrição** página, selecione a subscrição relevante.

    d) selecione **controlo de acesso (IAM)** e, em seguida, clique em **atribuições de funções**.

    e) clique **adicionar atribuição de função** para adicionar um utilizador com o nível de acesso relevantes. Os utilizadores que foram criados através do portal CSP são apresentados no separador de atribuições de função.

      ![Adicionar um utilizador](./media/vmware-azure-multi-tenant-csp-disaster-recovery/add-user-subscription.png)

- Na maioria das operações de gestão, o *contribuinte* função é suficiente. Os utilizadores com este nível de acesso podem fazer tudo numa assinatura exceto alteração de níveis de acesso (para os quais *proprietário*-é necessário o acesso de nível).
- Recuperação de sites, também tem três [predefinidos de funções de utilizador](site-recovery-role-based-linked-access-control.md), que podem ser utilizados para restringir ainda mais os níveis de acesso conforme necessário.

## <a name="multi-tenant-environments"></a>Ambientes de multi-inquilinos

Existem três modelos principais de multi-inquilinos:

* **Fornecedor de serviços de alojamento (HSP) partilhados**: O parceiro é o proprietário a infraestrutura física e utiliza partilhado recursos (vCenter, centros de dados, armazenamento físico e assim por diante) para alojar vários inquilinos VMs na mesma infraestrutura. O parceiro pode fornecer gestão de recuperação após desastre como um serviço gerido ou o inquilino pode ser proprietário recuperação após desastre como uma solução de self-service.

* **Fornecedor de serviços de alojamento de dedicado**: O parceiro é proprietário a infraestrutura física, mas usa recursos dedicados (vários vCenters, arquivos de dados físicos e assim por diante) para hospedar VMs de cada inquilino numa infraestrutura separada. O parceiro pode fornecer gestão de recuperação após desastre como um serviço gerido ou o inquilino pode possuí-lo como uma solução de self-service.

* **Fornecedor de serviços (MSP) gerido**: O cliente possui a infraestrutura física que aloja as VMs e o parceiro fornece à habilitação da recuperação após desastre e gestão.

Ao configurar subscrições de inquilino, conforme descrito neste artigo, pode começar rapidamente que permite aos clientes em qualquer um dos modelos de multi-inquilinos relevantes. Pode saber mais sobre os diferentes modelos de multi-inquilinos e habilitando-controlos de acesso no local [aqui](vmware-azure-multi-tenant-overview.md).

## <a name="next-steps"></a>Passos Seguintes
- Saiba mais sobre [controlo de acesso baseado em funções](site-recovery-role-based-linked-access-control.md) para gerir implementações do Azure Site Recovery.
- Saiba mais sobre o VMware para o Azure [arquitetura da replicação](vmware-azure-architecture.md).
- [Veja o tutorial](vmware-azure-tutorial.md) para replicar VMs de VMware para o Azure.
Saiba mais sobre [ambientes de multi-inquilinos](vmware-azure-multi-tenant-overview.md) para replicar VMs de VMware para o Azure.
