---
title: Criar um VM do Windows a partir de um VHD especializado no portal Azure
description: Crie um novo Windows VM a partir de um VHD no portal Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.collection: windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 01/18/2019
ms.author: cynthn
ms.openlocfilehash: a93ab207960133ecbf5a68927c5d16a6daf94fd4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102562219"
---
# <a name="create-a-vm-from-a-vhd-by-using-the-azure-portal"></a>Criar um VM a partir de um VHD utilizando o portal Azure

Existem várias formas de criar uma máquina virtual (VM) em Azure: 

- Se já tiver um disco rígido virtual (VHD) para utilizar ou se quiser copiar o VHD de um VM existente para utilizar, pode criar um novo VM *ligando* o VHD ao novo VM como um disco de oss. 

- Pode criar um novo VM a partir do VHD de um VM que foi eliminado. Por exemplo, se tiver um VM Azure que não esteja a funcionar corretamente, pode eliminar o VM e utilizar o seu VHD para criar um novo VM. Pode reutilizar o mesmo VHD ou criar uma cópia do VHD criando uma imagem instantânea e, em seguida, criar um novo disco gerido a partir do instantâneo. Embora a criação de um instantâneo dê mais alguns passos, preserva o VHD original e proporciona-lhe um recuo.

- Pegue num VM clássico e use o VHD para criar um novo VM que utiliza o modelo de implementação do Gestor de Recursos e discos geridos. Para obter os melhores resultados, **pare** o VM clássico no portal Azure antes de criar o instantâneo.
 
- Pode criar um VM Azure a partir de um VHD no local, carregando o VHD no local e ligando-o a um novo VM. Utiliza o PowerShell ou outra ferramenta para carregar o VHD numa conta de armazenamento e, em seguida, cria um disco gerido a partir do VHD. Para mais informações, consulte [upload de um VHD especializado.](create-vm-specialized.md#option-2-upload-a-specialized-vhd) 

> [!IMPORTANT]
> 
> Quando se utiliza um disco especializado para criar um novo VM, o novo VM mantém o nome de computador do VM original. Outras informações específicas do computador (por exemplo, CMID) também são mantidas e, em alguns casos, esta informação duplicada pode causar problemas. Ao copiar um VM, esteja ciente de que tipos de informações específicas do computador as suas aplicações dependem.  
> Assim, não utilize um disco especializado se quiser criar vários VMs. Em vez disso, para implementações maiores, [crie uma imagem](capture-image-resource.md) e, em seguida, use essa imagem para criar [vários VMs](create-vm-generalized-managed.md).

Recomendamos que limite o número de implementações simultâneas a 20 VMs a partir de um único instantâneo ou VHD. 

## <a name="copy-a-disk"></a>Copiar um disco

Crie uma imagem instantânea e, em seguida, crie um disco a partir do instantâneo. Esta estratégia permite-lhe manter o VHD original como um recuo:

1. A partir do [portal Azure,](https://portal.azure.com)no menu esquerdo, selecione **Todos os serviços**.
2. Na caixa de pesquisa de todos os  **serviços,** introduza discos e, em seguida, selecione Discos para exibir a lista de discos disponíveis. 
3. Selecione o disco que pretende utilizar. Aparece a página **do disco** para o disco.
4. A partir do menu no topo, **selecione Criar instantâneo**. 
5. Insira um **nome** para a foto.
6. Escolha um **grupo de recursos** para o instantâneo. Pode utilizar um grupo de recursos existente ou criar um novo.
7. Para **o tipo de conta,** escolha o armazenamento Standard **(HDD)** ou **Premium (SSD).**
8. Quando terminar, **selecione Criar** para criar o instantâneo.
9. Depois de ter sido criado o instantâneo, selecione **Criar um recurso** no menu esquerdo.
10. Na caixa de pesquisa, **introduza o disco gerido** e, em seguida, selecione **Discos Geridos** da lista.
11. Na página **Discos Geridos,** selecione **Criar**.
12. Introduza um **nome** para o disco.
13. Escolha um **grupo de recursos** para o disco. Pode utilizar um grupo de recursos existente ou criar um novo. Esta seleção também será usada como o grupo de recursos onde se cria o VM a partir do disco.
14. Para **o tipo de conta,** escolha o armazenamento Standard **(HDD)** ou **Premium (SSD).**
15. No **tipo Fonte,** certifique-se de que **o Snapshot** está selecionado.
16. No drop-down **do instantâneo Source,** selecione o instantâneo que pretende utilizar.
17. Faça quaisquer outros ajustes conforme necessário e, em seguida, **selecione Criar** para criar o disco.

## <a name="create-a-vm-from-a-disk"></a>Criar um VM a partir de um disco

Depois de ter o VHD de disco gerido que pretende utilizar, pode criar o VM no portal:

1. A partir do [portal Azure,](https://portal.azure.com)no menu esquerdo, selecione **Todos os serviços**.
2. Na caixa de pesquisa de todos os  **serviços,** introduza discos e, em seguida, selecione Discos para exibir a lista de discos disponíveis. 
3. Selecione o disco que pretende utilizar. A página **do disco** para o disco abre.
4. Na página **'Visão Geral',** certifique-se de que o **ESTADO DO DISCO** está listado como **Unattached**. Se não for, poderá ter de separar o disco do VM ou eliminar o VM para libertar o disco.
4. No menu no topo da página, selecione **Create VM**.
5. Na página Basics para o novo VM, **insira** um **nome de máquina Virtual** e selecione um grupo de **Recursos** existente ou crie um novo.
6. Para **tamanho**, selecione **Alterar tamanho** para aceder à página **Tamanho.**
7. Selecione uma linha de tamanho VM e, em seguida, escolha **Selecione**.
8. Na página **networking,** pode deixar o portal criar todos os novos recursos ou pode selecionar um grupo de **segurança virtual** e de **rede** existente. O portal cria sempre uma nova interface de rede e endereço IP público para o novo VM. 
9. Na página **Gestão,** escame quaisquer alterações às opções de monitorização.
10. Na página **de Configuração do Convidado,** adicione quaisquer extensões conforme necessário.
11. Quando terminar, selecione **Review + create**. 
12. Se a configuração VM passar na validação, **selecione Criar** para iniciar a implementação.


## <a name="next-steps"></a>Passos seguintes

Também pode utilizar o PowerShell para [carregar um VHD para Azure e criar um VM especializado.](create-vm-specialized.md)


