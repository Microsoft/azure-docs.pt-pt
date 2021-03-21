---
title: Confiem a recuperação de desastres VMware para a Azure num ambiente de vários arrendamentos utilizando o programa De recuperação de locais e o programa Cloud Solution Provider (CSP) | Microsoft Docs
description: Descreve como configurar a recuperação de desastres VMware num ambiente multi-inquilino com a Recuperação do Site Azure.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: mayg
ms.openlocfilehash: fb2a8a7bb14758ab21eb2183a119f456b53c8562
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97654954"
---
# <a name="set-up-vmware-disaster-recovery-in-a-multi-tenancy-environment-with-the-cloud-solution-provider-csp-program"></a>Configurar a recuperação de desastres VMware num ambiente de vários arrendamentos com o programa Cloud Solution Provider (CSP)

O [programa CSP](https://partner.microsoft.com/cloud-solution-provider) promove histórias melhores para os serviços na nuvem da Microsoft, incluindo o Microsoft 365, Enterprise Mobility Suite e Microsoft Azure. Com a CSP, os parceiros possuem a relação de ponta a ponta com os clientes, e tornam-se o principal ponto de contacto de relacionamento. Os parceiros podem implementar subscrições Azure para os clientes e combinar as subscrições com as suas próprias ofertas personalizadas de valor acrescentado.

Com [a Azure Site Recovery,](site-recovery-overview.md)como parceiros, pode gerir a recuperação de desastres para os clientes diretamente através da CSP. Em alternativa, pode utilizar a CSP para configurar ambientes de recuperação de locais e permitir que os clientes gerem as suas próprias necessidades de recuperação de desastres de forma self-service. Em ambos os cenários, os parceiros são a ligação entre a Recuperação do Site e os seus clientes. Os parceiros prestam serviço à relação com o cliente e faturam os clientes para o uso da Recuperação do Site.

Este artigo descreve como você como um parceiro pode criar e gerir subscrições de inquilinos através de CSP, para um cenário de replicação VMware multi-inquilino.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a replicação VMware, tem de fazer o seguinte:

- [Preparar](tutorial-prepare-azure.md) Recursos Azure, incluindo uma subscrição Azure, uma rede virtual Azure e uma conta de armazenamento.
- [Prepare](vmware-azure-tutorial-prepare-on-premises.md) os servidores VMware e VMs no local.
- Para cada inquilino, crie um servidor de gestão separado que possa comunicar com os VMs do inquilino e com os seus servidores vCenter. Apenas você, como parceiro, deve ter direitos de acesso a este servidor de gestão. Saiba mais sobre [ambientes multi-inquilinos.](vmware-azure-multi-tenant-overview.md)

## <a name="create-a-tenant-account"></a>Criar uma conta de inquilino

1. Através do [Microsoft Partner Center,](https://partnercenter.microsoft.com/)inscreva-se na sua conta CSP.
2. No menu **Dashboard,** selecione **Clientes.**
3. Na página que abre, clique no botão **Adicionar cliente.**
4. Na página **De Novo Cliente,** preencha os dados de informação da conta para o arrendatário.

    ![A página de Informação de Conta](./media/vmware-azure-multi-tenant-csp-disaster-recovery/customer-add-filled.png)

5. Em seguida, clique em **Seguinte: Subscrições**.
6. Na página de seleção de subscrições, selecione a caixa de verificação **Do Microsoft Azure.** Pode adicionar outras subscrições agora ou em qualquer outro momento.
7. Na página **'Rever',** confirmar os dados do inquilino e, em seguida, clicar **em Enviar.**
8. Depois de ter criado a conta do inquilino, aparece uma página de confirmação, apresentando os detalhes da conta padrão e a palavra-passe para essa subscrição. Guarde as informações e altere a palavra-passe mais tarde, se necessário, através da página de entrada do portal Azure.

Você pode compartilhar esta informação com o inquilino como está, ou você pode criar e compartilhar uma conta separada, se necessário.

## <a name="access-the-tenant-account"></a>Aceda à conta do inquilino

Pode aceder à subscrição do inquilino através do Painel do Microsoft Partner Center.

1. Na página **Clientes,** clique no nome da conta do inquilino.
2. Na página **de Subscrições** da conta do arrendatário, pode monitorizar as subscrições de conta existentes e adicionar mais subscrições, conforme necessário.
3. Para gerir as operações de recuperação de desastres do arrendatário, selecione **Todos os recursos (portal Azure)**. Isto dá-lhe acesso às assinaturas Azure do inquilino.

    ![A ligação todos os recursos](./media/vmware-azure-multi-tenant-csp-disaster-recovery/all-resources-select.png)  

4. Pode verificar o acesso clicando no link Azure Ative Directory no canto superior direito do portal Azure.

    ![Ligação Azure Ative Directory](./media/vmware-azure-multi-tenant-csp-disaster-recovery/aad-admin-display.png)

Pode agora realizar e gerir todas as operações de Recuperação do Local para o arrendatário no portal Azure. Para aceder à subscrição do inquilino através da CSP para recuperação de desastres gerido, siga o processo previamente descrito.

## <a name="assign-tenant-access-to-the-subscription"></a>Atribuir acesso ao inquilino à subscrição

1. Assegurar a criação da infraestrutura de recuperação de desastres. Os parceiros acedem a assinaturas de inquilinos através do portal CSP, independentemente de a recuperação de desastres ser gerida ou self-service. Instale o cofre e registe a infraestrutura para as assinaturas dos inquilinos.
1. Forneça ao inquilino a [conta que criou.](#create-a-tenant-account)
1. Pode adicionar um novo utilizador à subscrição do inquilino através do portal CSP da seguinte forma:

    1. Vá à página de subscrição do CSP do inquilino e, em seguida, selecione a opção **Utilizadores e licenças.**

       ![Página de assinatura CSP do inquilino](./media/vmware-azure-multi-tenant-csp-disaster-recovery/users-and-licences.png)

    1. Agora crie um novo utilizador introduzindo os detalhes relevantes e selecionando permissões, ou carregando a lista de utilizadores num ficheiro CSV.

    1. Depois de criar um novo utilizador, volte ao portal Azure. Na página **Subscrição,** selecione a subscrição relevante.

    1. Selecione **o controlo de acesso (IAM)** e, em seguida, clique em **atribuições de Função**.

    1. Clique **em Adicionar atribuição de função** para adicionar um utilizador ao nível de acesso relevante. Os utilizadores que foram criados através do portal CSP são apresentados no separador atribuições de Função.

        ![Adicionar um utilizador](./media/vmware-azure-multi-tenant-csp-disaster-recovery/add-user-subscription.png)

- Para a maioria das operações de gestão, o *papel de Contribuinte* é suficiente. Os utilizadores com este nível de acesso podem fazer tudo numa subscrição, exceto alterar os níveis de acesso (para os quais é necessário aceder ao nível *do Proprietário).*
- A Recuperação do Site também possui três [funções de utilizador predefinidas,](site-recovery-role-based-linked-access-control.md)que podem ser usadas para restringir ainda mais os níveis de acesso, conforme necessário.

## <a name="multi-tenant-environments"></a>Ambientes multi-inquilinos

Existem três grandes modelos multi-inquilinos:

* **Prestador de Serviços de Hospedagem Partilhada (HSP)**: O parceiro detém a infraestrutura física e utiliza recursos partilhados (vCenter, datacenters, armazenamento físico, e assim por diante) para hospedar vários VMs inquilinos na mesma infraestrutura. O parceiro pode fornecer a gestão de recuperação de desastres como um serviço gerido, ou o inquilino pode possuir a recuperação de desastres como uma solução de self-service.

* **Fornecedor de Serviços de Hospedagem Dedicado**: O parceiro detém a infraestrutura física, mas utiliza recursos dedicados (múltiplos vCenters, datastores físicos, e assim por diante) para hospedar os VMs de cada inquilino numa infraestrutura separada. O parceiro pode fornecer a gestão de recuperação de desastres como um serviço gerido, ou o inquilino pode possuí-lo como uma solução de self-service.

* **Prestador de Serviços Geridos (MSP)**: O cliente é dono da infraestrutura física que acolhe os VMs, e o parceiro fornece habilitação e gestão de recuperação de desastres.

Ao configurar subscrições de inquilinos como descrito neste artigo, pode rapidamente começar a ativar os clientes em qualquer um dos modelos relevantes multi-inquilinos. Você pode saber mais sobre os diferentes modelos multi-inquilinos e permitir controlos de acesso no local [aqui.](vmware-azure-multi-tenant-overview.md)

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [o controlo de acesso baseado em funções (Azure RBAC)](site-recovery-role-based-linked-access-control.md) para gerir as implementações de Recuperação do Local de Azure.
- Saiba mais sobre a arquitetura de [replicação](vmware-azure-architecture.md)VMware to Azure.
- [Reveja o tutorial](vmware-azure-tutorial.md) para replicar VMware VMs para Azure.
Saiba mais sobre [ambientes multi-inquilinos](vmware-azure-multi-tenant-overview.md) para replicar VMware VMs para Azure.
