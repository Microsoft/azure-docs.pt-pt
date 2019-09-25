---
title: Criar ativos técnicos para uma oferta de máquina virtual para o Azure Marketplace
description: Explica como criar os ativos técnicos para uma oferta de máquina virtual no Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
ms.service: marketplace
ms.topic: article
ms.date: 08/20/2018
ms.author: pabutler
ms.openlocfilehash: c1ef00f846dfad76629b0603ab79fba17249417c
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "71224533"
---
# <a name="create-technical-assets-for-a-virtual-machine-offer"></a>Criar ativos técnicos para uma oferta de máquina virtual

Esta seção orienta você pela criação e configuração dos ativos técnicos para uma oferta de VM (máquina virtual) para o Azure Marketplace.  Uma VM contém dois componentes: o VHD (disco rígido virtual) da solução e os discos de dados associados opcionais.  

- *VHDs (discos rígidos virtuais)* , contendo o sistema operacional e sua solução, que serão implantados com sua oferta do Azure Marketplace. O processo de preparação do VHD difere dependendo de se trata-se de uma VM baseada em Linux, baseada em Windows ou personalizada.
- *Os discos de dados* representam o armazenamento persistente e dedicado para uma máquina virtual. *Não* use o VHD da solução (por exemplo, a `C:` unidade) para armazenar informações persistentes.

Uma imagem de VM contém um disco do sistema operacional e zero ou mais discos de dados. Um VHD é necessário por disco. Até mesmo discos de dados em branco exigem a criação de um VHD.
Você deve configurar o sistema operacional da VM, o tamanho da VM, as portas a serem abertas e até 15 discos de dados anexados.

> [!TIP] 
> Independentemente do sistema operativo utilizado, adicione apenas o número mínimo de discos de dados necessários ao SKU. Os clientes não podem remover discos que fazem parte de uma imagem no momento da implantação, mas podem sempre adicionar discos durante ou após a implantação. 

> [!IMPORTANT]
> *Não altere a contagem de discos em uma nova versão de imagem.* Se for necessário reconfigurar os discos de dados na imagem, defina uma nova SKU. A publicação de uma nova versão de imagem com contagens de disco diferentes terá o potencial de interromper a nova implantação com base na nova versão da imagem em casos de dimensionamento automático, implantações automáticas de soluções por meio de modelos de Azure Resource Manager e outros cenários.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="fundamental-technical-knowledge"></a>Conhecimento técnico fundamental

Projetar, criar e testar esses ativos leva tempo e exige conhecimento técnico da plataforma Azure e das tecnologias usadas para criar a oferta. Além do seu domínio de solução, sua equipe de engenharia deve ter conhecimento das seguintes tecnologias da Microsoft: 
-   Noções básicas sobre os [Serviços do Azure](https://azure.microsoft.com/services/) 
-   Como [projetar e arquitetar aplicativos do Azure](https://azure.microsoft.com/solutions/architecture/)
-   Conhecimento de trabalho de [máquinas virtuais do Azure](https://azure.microsoft.com/services/virtual-machines/), [armazenamento do Azure](https://azure.microsoft.com/services/?filter=storage) e [rede do Azure](https://azure.microsoft.com/services/?filter=networking)
-   Conhecimento de trabalho do [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
-   Conhecimento de trabalho do [JSON](https://www.json.org/)


## <a name="suggested-tools"></a>Ferramentas sugeridas 

Escolha um ou ambos os seguintes ambientes de script para ajudar a gerenciar VHDs e VMs:
-   [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
-   [CLI do Azure](https://docs.microsoft.com/cli/azure)

Além disso, é recomendável adicionar as seguintes ferramentas ao seu ambiente de desenvolvimento: 

-   [Explorador do Armazenamento do Azure](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
-   [Visual Studio Code](https://code.visualstudio.com/)
    *   Extensão [Ferramentas de Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    *   Extensão [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    *   Extensão [Melhorar JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Também sugerimos a revisão das ferramentas disponíveis na página [ferramentas para desenvolvedores do Azure](https://azure.microsoft.com/tools/) e, se você estiver usando o Visual Studio, a [Visual Studio Marketplace](https://marketplace.visualstudio.com/).


## <a name="next-steps"></a>Passos seguintes

Os artigos subsequentes nesta seção orientam você pelas etapas de criação e registro desses ativos de VM:

1. [Criar um disco rígido virtual compatível](./cpp-create-vhd.md) com o Azure explica como criar um VHD baseado em Linux ou Windows que seja compatível com o Azure.  Ele inclui práticas recomendadas, como dimensionamento, aplicação de patches e preparação da VM para carregamento.

2. [Conectar-se à máquina virtual](./cpp-connect-vm.md) explica como conectar-se remotamente à sua VM recém-criada e entrar nela.  Este artigo também explica como interromper a VM para economizar em custos de uso.

3. [Configurar a máquina virtual](./cpp-configure-vm.md) explica como escolher o tamanho correto do VHD, generalizar sua imagem, aplicar atualizações recentes (patches) e agendar configurações personalizadas.

4. [Implantar uma máquina virtual de um disco rígido virtual](./cpp-deploy-vm-vhd.md) explica como registrar uma VM de um VHD implantado pelo Azure.  Ele lista as ferramentas necessárias e como usá-las para criar uma imagem de VM do usuário e, em seguida, implantá-la no Azure usando os scripts do [portal do Microsoft Azure](https://ms.portal.azure.com/) ou do PowerShell. 

5. [Certificar uma imagem de máquina virtual](./cpp-certify-vm.md) explica como testar e enviar uma imagem de VM para a certificação do Azure Marketplace. Ele explica onde obter a ferramenta *de teste de certificação para a ferramenta certificada do Azure* e como usar essa ferramenta para certificar a imagem da VM. 

6. [Obter URI de SAS](./cpp-get-sas-uri.md) explica como obter o URI de SAS (assinatura de acesso compartilhado) para suas imagens de VM.
 
Como um artigo de suporte, [problemas comuns de URL de assinatura de acesso compartilhado](./cpp-common-sas-url-issues.md) lista alguns problemas comuns que você pode encontrar usando URIS de SAS e as possíveis soluções correspondentes.

Depois de concluir todas essas etapas, você estará pronto para [publicar sua oferta de VM](./cpp-publish-offer.md) no Azure Marketplace.
