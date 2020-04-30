---
title: Criar ativos técnicos para uma oferta de máquina virtual para o Mercado Azure
description: Explica como criar os ativos técnicos para uma oferta de máquina virtual no Mercado Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/20/2018
ms.author: dsindona
ms.openlocfilehash: c6ca017e18a83f0745140cba15d9c894e5a21c32
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82148128"
---
# <a name="create-technical-assets-for-a-virtual-machine-offer"></a>Criar ativos técnicos para uma oferta de máquina virtual

> [!IMPORTANT]
> A partir de 13 de abril de 2020, iniciaremos a gestão móvel das ofertas da sua Máquina Virtual Azure ao Partner Center. Após a migração, você vai criar e gerir suas ofertas no Partner Center. Siga as instruções em [Create your Azure Virtual Machine ativos técnicos](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-create-offer) para gerir as suas ofertas migradas.

Esta secção acompanha-o através da criação e configuração dos ativos técnicos para uma oferta de máquina virtual (VM) para o Azure Marketplace.  Um VM contém dois componentes: a solução disco rígido virtual (VHD) e os discos de dados associados opcionais.  

- *Discos rígidos virtuais (VHDs)* contendo o sistema operativo e a sua solução, que irá implementar com a sua oferta do Azure Marketplace. O processo de preparação do VHD difere dependendo se se trata de um VM baseado em Linux, baseado no Windows ou num VM personalizado.
- *Os discos* de dados representam um armazenamento dedicado e persistente para uma máquina virtual. Não *not* utilize a solução VHD `C:` (por exemplo, a unidade) para armazenar informações persistentes.

Uma imagem VM contém um disco de sistema operativo e discos de dados zero ou mais. Um VHD é necessário por disco. Mesmo discos de dados em branco requerem a criação de um VHD.
Tem de configurar o VM OS, o tamanho VM, as portas para abrir e até 15 discos de dados anexados.

> [!TIP] 
> Independentemente do sistema operativo que utilize, adicione apenas o número mínimo de discos de dados necessários pelo SKU. Os clientes não podem remover discos que façam parte de uma imagem no momento da implantação, mas podem sempre adicionar discos durante ou após a implantação. 

> [!IMPORTANT]
> *Não altere a contagem de discos numa nova versão de imagem.* Se tiver de reconfigurar os discos de dados na imagem, defina um novo SKU. A publicação de uma nova versão de imagem com diferentes contagens de discos terá o potencial de quebrar uma nova implementação com base na nova versão de imagem em casos de escala automática, implementações automáticas de soluções através de modelos do Gestor de Recursos do Azure e outros cenários.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="fundamental-technical-knowledge"></a>Conhecimento técnico fundamental

Projetar, construir e testar estes ativos leva tempo e requer conhecimentos técnicos tanto da plataforma Azure como das tecnologias usadas para construir a oferta. Além do seu domínio de solução, a sua equipa de engenharia deve ter conhecimento das seguintes tecnologias da Microsoft: 
-    Compreensão básica dos [Serviços Azure](https://azure.microsoft.com/services/) 
-    Como [conceber e arquiteto sintetizar aplicações Azure](https://azure.microsoft.com/solutions/architecture/)
-    Conhecimento de trabalho de [Máquinas Virtuais Azure,](https://azure.microsoft.com/services/virtual-machines/) [Armazenamento Azure](https://azure.microsoft.com/services/?filter=storage) e [Networking Azure](https://azure.microsoft.com/services/?filter=networking)
-    Conhecimento de trabalho do Gestor de [Recursos Azure](https://azure.microsoft.com/features/resource-manager/)
-    Conhecimento de Trabalho da [JSON](https://www.json.org/)


## <a name="suggested-tools"></a>Ferramentas sugeridas 

Escolha um ou ambos os seguintes ambientes de scripts para ajudar a gerir VHDs e VMs:
-    [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
-    [CLI do Azure](https://docs.microsoft.com/cli/azure)

Além disso, recomendamos adicionar as seguintes ferramentas ao seu ambiente de desenvolvimento: 

-    [Explorador do Storage do Azure](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
-    [Visual Studio Code](https://code.visualstudio.com/)
    *    Extensão: Ferramentas de [Gestor de Recursos Azure](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    *    Extensão: [Embelezar](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    *    Extensão: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Sugerimos também rever as ferramentas disponíveis na página [Azure Developer Tools](https://azure.microsoft.com/tools/) e, se estiver a utilizar o Visual Studio, o [Visual Studio Marketplace.](https://marketplace.visualstudio.com/)


## <a name="next-steps"></a>Passos seguintes

Os artigos subsequentes nesta secção passam por você através dos passos de criação e registo destes ativos VM:

1. [Criar um disco rígido virtual compatível com Azure](./cpp-create-vhd.md) explica como criar um VHD baseado em Linux ou Windows que seja compatível com o Azure.  Inclui boas práticas, tais como dimensionamento, remendos e preparação do VM para o upload.

2. [A ligação à máquina virtual](./cpp-connect-vm.md) explica como ligar remotamente ao seu VM recém-criado e iniciar sessão.  Este artigo também explica como parar o VM para economizar nos custos de utilização.

3. [Configurar a máquina virtual](./cpp-configure-vm.md) explica como escolher o tamanho VHD correto, generalizar a sua imagem, aplicar atualizações recentes (patches) e agendar configurações personalizadas.

4. [Implementar uma máquina virtual a partir de um disco rígido virtual](./cpp-deploy-vm-vhd.md) explica como registar um VM a partir de um VHD implantado em Azure.  Ele lista as ferramentas necessárias e como usá-las para criar uma imagem VM do utilizador, em seguida, implementá-la para Azure usando o [portal Microsoft Azure](https://ms.portal.azure.com/) ou scripts PowerShell. 

5. [Certifica uma imagem de máquina virtual](./cpp-certify-vm.md) explica como testar e submeter uma imagem VM para certificação Azure Marketplace. Explica onde obter a ferramenta de teste de certificação para a ferramenta *Azure Certified* e como usar esta ferramenta para certificar a sua imagem VM. 

6. [O Get SAS URI](./cpp-get-sas-uri.md) explica como obter a assinatura de acesso partilhado (SAS) URI para as suas imagens VM.
 
Como um artigo de apoio, os problemas de URL de assinatura partilhada common listam [alguns problemas](./cpp-common-sas-url-issues.md) comuns que pode encontrar usando URIs SAS e as soluções possíveis correspondentes.

Depois de ter concluído todos estes passos, estará pronto para publicar a [sua oferta VM](./cpp-publish-offer.md) no Azure Marketplace.
