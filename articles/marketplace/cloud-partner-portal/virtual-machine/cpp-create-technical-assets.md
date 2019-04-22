---
title: Criar ativos técnicos para uma oferta de máquina virtual para o Azure Marketplace | Documentos da Microsoft
description: Explica como criar os recursos técnicos para uma oferta de máquina virtual no Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 08/20/2018
ms.author: pbutlerm
ms.openlocfilehash: 6f1a93c3d3059e612d8c309b263e263dbb84c67f
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59050106"
---
# <a name="create-technical-assets-for-a-virtual-machine-offer"></a>Criar ativos técnicos para uma oferta de máquina virtual

Esta secção explica como criar e configurar os recursos técnicos para uma oferta de máquina virtual (VM) para o Azure Marketplace.  Uma VM contém dois componentes: o solução de disco rígido virtual (VHD) e os discos de dados associados opcional.  

- *Discos rígidos virtuais (VHDs)*, que contém o sistema operativo e a sua solução, que irá implementar na sua oferta do Azure Marketplace. O processo de preparar o VHD é diferente consoante seja um baseado em Linux, com base em Windows ou uma VM baseada em personalizado.
- *Discos de dados* representam armazenamento dedicado e persistente para uma máquina virtual. Fazer *não* utilizar a solução de VHD (por exemplo, o `C:` unidade) para armazenar informações persistentes.

Uma imagem VM contém o disco de um sistema operativo e zero ou mais discos de dados. É necessário um VHD por disco. Os discos de dados, mesmo em branco precisam de um VHD a ser criada.
Tem de configurar o SO da VM, o tamanho da VM, as portas para abrir e até 15 de discos de dados anexados.

> [!TIP] 
> Independentemente do sistema operativo utilizado, adicione apenas o número mínimo de discos de dados necessários ao SKU. Os clientes não é possível remover os discos que fazem parte de uma imagem no momento da implementação, mas eles sempre adicionar discos durante ou após a implantação. 

> [!IMPORTANT]
> *Não altere o número de discos numa nova versão de imagem.* Se tem de reconfigurar discos de dados na imagem, defina um novo SKU. Publicar uma nova versão de imagem com contagens de outro disco terão o potencial de interromper a nova implementação com base na nova versão de imagem em casos de Implantações de dimensionamento automático e automática de soluções através de modelos Azure Resource Manager e outros cenários.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="fundamental-technical-knowledge"></a>Conhecimentos técnicos fundamentais

Estruturar, criar e testar esses ativos demorar algum tempo e requer conhecimento técnico de plataforma do Azure e as tecnologias usadas para criar a oferta. Além de seu domínio de solução, a sua equipa de engenharia deve ter conhecimento sobre as seguintes tecnologias da Microsoft: 
-   Noções básicas sobre [dos serviços do Azure](https://azure.microsoft.com/services/) 
-   Como [de design e a arquitetura de aplicações do Azure](https://azure.microsoft.com/solutions/architecture/)
-   Conhecimento prático do [máquinas virtuais do Azure](https://azure.microsoft.com/services/virtual-machines/), [armazenamento do Azure](https://azure.microsoft.com/services/?filter=storage) e [redes do Azure](https://azure.microsoft.com/services/?filter=networking)
-   Conhecimento prático do [do Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
-   Conhecimento prático do [JSON](https://www.json.org/)


## <a name="suggested-tools"></a>Sugestões de ferramentas 

Escolha um ou ambos dos seguintes ambientes de criação de scripts para ajudar a gerir VHDs e VMs:
-   [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
-   [CLI do Azure](https://docs.microsoft.com/cli/azure)

Além disso, é recomendável adicionar as seguintes ferramentas ao seu ambiente de desenvolvimento: 

-   [Explorador do Armazenamento do Azure](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
-   [Visual Studio Code](https://code.visualstudio.com/)
    *   Extensão: [Ferramentas do Gestor de recursos do Azure](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    *   Extensão: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    *   Extensão: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Sugerimos também a rever as ferramentas disponíveis no [ferramentas de programação do Azure](https://azure.microsoft.com/tools/) página e, se estiver a utilizar o Visual Studio, o [Visual Studio Marketplace](https://marketplace.visualstudio.com/).


## <a name="next-steps"></a>Passos Seguintes

Os artigos subseqüentes nesta secção guiá-lo pelos passos de criar e registar estes recursos de VM:

1. [Criar um disco rígido virtual compatível com o Azure](./cpp-create-vhd.md) explica como criar qualquer um ou Windows baseado no Linux VHD que é compatível com o Azure.  Ele inclui práticas recomendadas, como o dimensionamento, a aplicação de patches e a preparar a VM para carregar.

2. [Ligar à máquina virtual](./cpp-connect-vm.md) explica como ligar à sua VM criada recentemente e inicie sessão remotamente.  Este artigo também explica como parar a VM para poupar nos custos de utilização.

3. [Configurar a máquina virtual](./cpp-configure-vm.md) explica como escolher o tamanho do VHD correto, generalizar a imagem, aplicar as atualizações mais recentes (correções) e agendar configurações personalizadas.

4. [Implementar uma máquina virtual a partir de um disco rígido virtual](./cpp-deploy-vm-vhd.md) explica como pode registar uma VM a partir de um VHD implementadas no Azure.  Ele lista as ferramentas necessárias e como utilizá-los para criar uma imagem de VM do utilizador, em seguida, implementá-la para o Azure através da [portal do Microsoft Azure](https://ms.portal.azure.com/) ou scripts do PowerShell. 

5. [Certificar a uma imagem de máquina virtual](./cpp-certify-vm.md) explica como testar e enviar uma imagem de VM para a certificação do Azure Marketplace. Explica onde obter o *ferramenta de teste de certificação para o Azure Certified* ferramenta e como utilizar esta ferramenta para certificar a sua imagem VM. 

6. [Obter o URI de SAS](./cpp-get-sas-uri.md) explica como obter a assinatura de acesso partilhado (SAS) URI para as imagens de VM.
 
Como um artigo de suporte [comum partilhado problemas de URL de assinatura de acesso](./cpp-common-sas-url-issues.md) apresenta uma lista de alguns problemas comuns que se possa deparar através dos URIs de SAS e possíveis soluções correspondentes.

Depois de concluir todas essas etapas, estará pronto para [publicar a sua oferta VM](./cpp-publish-offer.md) no Azure Marketplace.
