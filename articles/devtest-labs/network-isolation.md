---
title: Isolamento de rede em Azure DevTest Labs
description: Conheça o isolamento da rede em Azure DevTest Labs.
ms.topic: article
ms.date: 08/25/2020
ms.openlocfilehash: fbd2725cd3677e03cadbe0ae1f060b141f5d212b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88875965"
---
# <a name="network-isolation-in-devtest-labs"></a>Isolamento de rede em Laboratórios DevTest

Uma [rede virtual Azure](../virtual-network/virtual-networks-overview.md) funciona como uma fronteira de segurança, isolando os seus recursos Azure da internet pública. Também pode juntar uma rede virtual Azure à sua rede no local para poder ligar-se de forma segura aos seus recursos on-prem. Na DevTest Labs, pode optar por isolar todas as máquinas e [ambientes virtuais de laboratório](devtest-lab-configure-vnet.md) na sua rede [para](connect-environment-lab-virtual-network.md) garantir que os recursos de laboratório sigam as políticas de networking organizacional. 

Como proprietário de laboratório, também pode optar por isolar completamente o laboratório, o que significa que, ao lado de isolar máquinas virtuais e ambientes para a rede selecionada, também pode isolar a conta de armazenamento de laboratório e os cofres-chave criados na sua subscrição. Este artigo acompanha-o através da criação de um laboratório isolado em rede. 

Reveja também os seguintes artigos:

- [Como a DevTest Labs usa a conta de armazenamento de laboratório](encrypt-storage.md)
- [Como a DevTest Labs usa cofres-chave](devtest-lab-store-secrets-in-key-vault.md)
 
> [!NOTE]
> O isolamento da rede é atualmente suportado apenas para novas criações de laboratórios.

## <a name="steps-to-enable-network-isolation-during-lab-creation"></a>Passos para permitir o isolamento da rede durante a criação de laboratório

1. Durante a criação de laboratório, vá ao **separador networking.**
1. Pode selecionar uma rede **predefinitiva** que o laboratório irá criar para si ou selecionar uma rede existente a partir do drop-down. Só poderá selecionar redes que estejam na mesma região e subscrição que a do laboratório. 

    > [!div class="mx-imgBorder"]
    > ![Criar laboratório](./media/network-isolation/create-lab.png)
1. Selecione uma sub-rede.

    > [!div class="mx-imgBorder"]
    > ![Criar uma sub-rede](./media/network-isolation/create-lab-subnet.png)
1. Se optar por isolar os recursos do laboratório (conta de armazenamento de laboratório e cofre chave) para a rede padrão, não são necessárias mais medidas e o laboratório tratará de isolar os recursos que vão para a frente.
 
    > [!div class="mx-imgBorder"]
    > ![Isolamento da rede](./media/network-isolation/isolate-lab-resources.png)
1. Se optar por isolar os recursos do laboratório (conta de armazenamento de laboratório e cofre de chaves) para uma rede existente que selecionou, então precisa completar os seguintes passos após a criação do laboratório para garantir que o laboratório continua a funcionar no modo isolado. 
 
    > [!div class="mx-imgBorder"]
    > ![Isolar recursos](./media/network-isolation/isolate-my-vnet.png)

    > [!IMPORTANT]
    > O dono do laboratório precisa de completar estes passos antes de configurar e/ou criar quaisquer recursos no laboratório.

### <a name="steps-to-follow-post-lab-creation"></a>Passos para seguir a criação de pós-laboratório

1. Na página inicial do laboratório, selecione o **grupo de recursos** na página **'Vista Geral'.** Deve ver a página do **grupo de recursos** para o grupo de recursos que contém o laboratório. 
 
   > [!div class="mx-imgBorder"]
   > ![Laboratório Contoso](./media/network-isolation/contoso-lab.png)
1. Selecione a conta de armazenamento Azure do laboratório. A convenção de nomeação para a conta de armazenamento de laboratório é: um *<labNameWithoutInvalidCharacters* > *<número de 4 dígitos*>. Por exemplo, se o nome do laboratório for contosolab, o nome da conta de armazenamento pode ser acontosolab1234.
 
   > [!div class="mx-imgBorder"]
   > ![Teste contoso](./media/network-isolation/contoso-test.png)
1. Na conta de armazenamento, vá a Firewalls e redes virtuais e certifique-se de que a caixa de verificação 'Permitir serviços fidedignos da Microsoft para aceder a esta conta de armazenamento' é verificada. Como [o DevTest Labs é um serviço de confiança da Microsoft,](https://docs.microsoft.com/azure/storage/common/storage-network-security#trusted-microsoft-services)esta opção permitirá ao laboratório operar normalmente num modo isolado de rede. 

   > [!div class="mx-imgBorder"]
   > ![Firewalls de laboratório contoso](./media/network-isolation/contoso-lab-firewalls-vnets.png)
1. Em seguida, clique em **+Adicionar a rede virtual existente,** selecione a rede virtual e a sub-rede que escolheu enquanto cria o laboratório e clique em **Enable**. 

   > [!div class="mx-imgBorder"]
   > ![Contoso my vnet](./media/network-isolation/contoso-lab-my-vnet.png)
5.  Uma vez que o ponto final de serviço esteja habilitado com sucesso para a rede virtual selecionada, clique em **Adicionar**. 

   > [!div class="mx-imgBorder"]
   > ![Adicionar](./media/network-isolation/contoso-firewall-add.png)
 
Com isto, o armazenamento Azure permitirá ligações de entrada a partir da rede virtual adicionada e permitirá ao laboratório operar com sucesso num modo isolado de rede. 

Também pode optar por automatizar estes passos para configurar esta definição para vários laboratórios. 

[Saiba mais sobre a gestão das regras de acesso à rede padrão para armazenamento Azure usando PowerShell e CLI](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=/azure/virtual-network/toc.json#powershell)

## <a name="things-to-remember-while-using-a-lab-in-a-network-isolated-mode"></a>Coisas para lembrar ao usar um laboratório em um modo isolado de rede

### <a name="accessing-labs-storage-account-outside-the-lab"></a>Aceder à conta de armazenamento do laboratório fora do laboratório 

Dentro de um laboratório isolado de rede, para ações como o upload de um VHD para a conta de armazenamento do laboratório para criar imagens personalizadas, o proprietário do laboratório terá de permitir explicitamente o acesso à conta de armazenamento a partir de um ponto final permitido. Pode fazê-lo criando uma máquina virtual e acedendo de forma segura à conta de armazenamento do laboratório a partir dessa máquina virtual. 

[Saiba mais sobre o acesso a uma conta de armazenamento privadamente a partir de uma máquina virtual](../private-link/create-private-endpoint-storage-portal.md)

### <a name="exporting-usage-data-from-the-lab"></a>Exportação de dados de utilização do laboratório 

Dentro de um laboratório isolado de rede, para [exportar dados de uso pessoal para o laboratório, o](personal-data-delete-export.md)proprietário do laboratório precisará explicitamente de fornecer conta de armazenamento e gerar uma bolha dentro da conta para armazenar os dados. 

Se não for fornecida uma conta de armazenamento, esta operação falhará no modo isolado da rede, uma vez que a conta de armazenamento do laboratório não está acessível para o laboratório a utilizar em caso de não ter nenhuma conta de armazenamento fornecida pelo cliente. 

[Saiba mais sobre a exportação de dados de utilização de laboratório numa conta de armazenamento especificada](personal-data-delete-export.md#azure-powershell)

## <a name="next-steps"></a>Passos seguintes

[Criar ou modificar laboratórios automaticamente utilizando modelos de Gestor de Recursos Azure e PowerShell](devtest-lab-use-arm-and-powershell-for-lab-resources.md)
