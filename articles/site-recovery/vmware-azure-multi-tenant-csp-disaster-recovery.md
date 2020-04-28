---
title: Instale a recuperação de desastres vMware para o Azure num ambiente multi-arrendamento utilizando o programa De recuperação do Site e o Fornecedor de Soluções cloud (CSP) [ CSP) [ Microsoft Docs
description: Descreve como configurar a recuperação de desastres vMware em um ambiente multi-inquilino com Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: mayg
ms.openlocfilehash: 77b64f09b7fd1429eb23c4407c729dfc0aafdf2b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "60461030"
---
# <a name="set-up-vmware-disaster-recovery-in-a-multi-tenancy-environment-with-the-cloud-solution-provider-csp-program"></a>Configurar a recuperação de desastres vMware num ambiente multi-arrendamento com o programa Cloud Solution Provider (CSP)

O [programa CSP](https://partner.microsoft.com/en-US/cloud-solution-provider) promove histórias mais reunidas para serviços de nuvem da Microsoft, incluindo office 365, Enterprise Mobility Suite e Microsoft Azure. Com a CSP, os parceiros possuem a relação de ponta a ponta com os clientes, e tornam-se o principal ponto de contacto da relação. Os parceiros podem implementar subscrições azure para clientes, e combinar as subscrições com as suas próprias ofertas personalizadas de valor acrescentado.

Com a [Azure Site Recovery](site-recovery-overview.md), como parceiros pode gerir a recuperação de desastres para os clientes diretamente através da CSP. Alternadamente, pode utilizar o CSP para configurar ambientes de recuperação do site e permitir que os clientes gerem as suas próprias necessidades de recuperação de desastres de forma autosserviço. Em ambos os cenários, os parceiros são a ligação entre a Recuperação do Site e os seus clientes. Os parceiros estão a atender a relação com o cliente e faturam clientes para o uso da Recuperação do Site.

Este artigo descreve como você como parceiro pode criar e gerir subscrições de inquilinos através de CSP, para um cenário de replicação VMware multi-inquilino.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a replicação vMware, tem de fazer o seguinte:

- [Preparar](tutorial-prepare-azure.md) Os recursos do Azure, incluindo uma subscrição Azure, uma rede virtual Azure e uma conta de armazenamento.
- [Prepare](vmware-azure-tutorial-prepare-on-premises.md) no local servidores VMware e VMs.
- Para cada inquilino, crie um servidor de gestão separado que possa comunicar com os VMs do inquilino e os seus servidores vCenter. Só você, como parceiro, deve ter direitos de acesso a este servidor de gestão. Saiba mais sobre [ambientes multi-inquilinos.](vmware-azure-multi-tenant-overview.md)

## <a name="create-a-tenant-account"></a>Criar uma conta de inquilino

1. Através do [Microsoft Partner Center,](https://partnercenter.microsoft.com/)inscreva-se na sua conta CSP.
2. No menu **dashboard,** selecione **Clientes**.
3. Na página que abre, clique no botão adicionar do **cliente.**
4. Na página **do Novo Cliente,** preencha os dados da conta para o inquilino.

    ![A página informação da conta](./media/vmware-azure-multi-tenant-csp-disaster-recovery/customer-add-filled.png)

5. Em seguida, clique **em Seguinte: Subscrições**.
6. Na página de seleção de subscrições, selecione caixa de verificação **Microsoft Azure.** Pode adicionar outras subscrições agora ou em qualquer outro momento.
7. Na página **'Rever',** confirme os detalhes do inquilino e, em seguida, clique em **Submeter**.
8. Depois de ter criado a conta de inquilino, aparece uma página de confirmação, mostrando os detalhes da conta predefinida e a palavra-passe para essa subscrição. Guarde a informação e altere a palavra-passe mais tarde, se necessário, através da página de entrada do portal Azure.

Você pode compartilhar esta informação com o inquilino como está, ou você pode criar e partilhar uma conta separada, se necessário.

## <a name="access-the-tenant-account"></a>Aceda à conta de inquilino

Pode aceder à subscrição do inquilino através do Microsoft Partner Center Dashboard.

1. Na página **Clientes,** clique no nome da conta de inquilino.
2. Na página de **Subscrições** da conta de inquilino, pode monitorizar as subscrições de conta existentes e adicionar mais subscrições, conforme necessário.
3. Para gerir as operações de recuperação de desastres do inquilino, selecione **Todos os recursos (portal Azure)**. Isto dá-lhe acesso às subscrições do Azure do inquilino.

    ![A ligação All Resources](./media/vmware-azure-multi-tenant-csp-disaster-recovery/all-resources-select.png)  

4. Pode verificar o acesso clicando no link Azure Ative Diretório no canto superior direito do portal Azure.

    ![Ligação azure ative diretório](./media/vmware-azure-multi-tenant-csp-disaster-recovery/aad-admin-display.png)

Agora pode realizar e gerir todas as operações de Recuperação do Site para o inquilino no portal Azure. Para aceder à subscrição do arrendatário através de CSP para recuperação de desastres geridos, siga o processo previamente descrito.

## <a name="assign-tenant-access-to-the-subscription"></a>Atribuir acesso ao inquilino à subscrição

1. Certifique-se de que a infraestrutura de recuperação de desastres está montada. Os parceiros acedem às subscrições dos inquilinos através do portal CSP, independentemente de a recuperação de desastres ser gerida ou de self-service. Instale o cofre e registe a infraestrutura para as assinaturas dos inquilinos.
2. Forneça ao inquilino a [conta que criou.](#create-a-tenant-account)
3. Pode adicionar um novo utilizador à subscrição do arrendatário através do portal CSP da seguinte forma:

    a) Vá à página de subscrição do CSP do inquilino e, em seguida, selecione a opção **Utilizadores e licenças.**

      ![A página de subscrição do CSP do inquilino](./media/vmware-azure-multi-tenant-csp-disaster-recovery/users-and-licences.png)

    b) Agora crie um novo utilizador, inserindo os detalhes relevantes e selecionando permissões, ou fazendo o upload da lista de utilizadores num ficheiro CSV.
    
    c) Depois de ter criado um novo utilizador, volte ao portal Azure. Na página **de Subscrição,** selecione a subscrição relevante.

    d) Selecione o controlo de **acesso (IAM)** e, em seguida, clique em atribuições de **funções**.

    e) Clique em Adicionar a atribuição de **funções** para adicionar um utilizador ao nível de acesso relevante. Os utilizadores que foram criados através do portal CSP são apresentados no separador de atribuições Role.

      ![Adicionar um utilizador](./media/vmware-azure-multi-tenant-csp-disaster-recovery/add-user-subscription.png)

- Para a maioria das operações de gestão, o papel *do Contribuinte* é suficiente. Os utilizadores com este nível de acesso podem fazer tudo numa subscrição, exceto alterar os níveis de acesso (para os quais é necessário o acesso ao nível do *Proprietário).*
- A Recuperação do Site também tem três [funções predefinidas](site-recovery-role-based-linked-access-control.md)de utilizador, que podem ser usadas para restringir ainda mais os níveis de acesso, conforme necessário.

## <a name="multi-tenant-environments"></a>Ambientes multi-inquilinos

Existem três grandes modelos multi-inquilinos:

* Fornecedor de Serviços de **Hospedagem Partilhada (HSP)**: O parceiro é dono da infraestrutura física, e utiliza recursos partilhados (vCenter, datacenters, armazenamento físico, e assim por diante) para acolher vários VMs de inquilinos na mesma infraestrutura. O parceiro pode fornecer a gestão de recuperação de desastres como um serviço gerido, ou o inquilino pode possuir a recuperação de desastres como uma solução de self-service.

* Provedor dedicado de Serviços de **Hospedagem**: O parceiro é dono da infraestrutura física, mas utiliza recursos dedicados (múltiplos vCenters, datastores físicas, e assim por diante) para acolher os VMs de cada inquilino numa infraestrutura separada. O parceiro pode fornecer a gestão de recuperação de desastres como um serviço gerido, ou o inquilino pode possuí-lo como uma solução de self-service.

* **Prestador de serviços geridos (MSP)**: O cliente é dono da infraestrutura física que acolhe os VMs, e o parceiro fornece habilitação e gestão de recuperação de desastres.

Ao configurar subscrições de inquilinos, conforme descrito neste artigo, pode rapidamente começar a permitir aos clientes em qualquer um dos modelos multi-inquilinos relevantes. Você pode aprender mais sobre os diferentes modelos multi-inquilinos e ativar controlos de acesso no local [aqui](vmware-azure-multi-tenant-overview.md).

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [o controlo de acesso baseado em papéis](site-recovery-role-based-linked-access-control.md) para gerir as implementações de Recuperação de Sites Azure.
- Saiba mais sobre vMware para a arquitetura de [replicação](vmware-azure-architecture.md)Azure.
- [Reveja o tutorial](vmware-azure-tutorial.md) para replicar VMware VMs para Azure.
Saiba mais sobre [ambientes multi-inquilinos](vmware-azure-multi-tenant-overview.md) para replicar VMware VMs para Azure.
