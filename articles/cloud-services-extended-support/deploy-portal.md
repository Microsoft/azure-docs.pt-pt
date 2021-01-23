---
title: Implementar um Serviço de Nuvem Azure (suporte alargado) - Portal Azure
description: Implementar um Serviço de Nuvem Azure (suporte alargado) utilizando o portal Azure
ms.topic: tutorial
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 9ca0a3852c6be04332369e57ce1916e5f589516f
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98745152"
---
# <a name="deploy-azure-cloud-services-extended-support-using-the-azure-portal"></a>Implementar serviços de nuvem Azure (suporte alargado) utilizando o portal Azure
Este artigo explica como usar o portal Azure para criar uma implementação de Cloud Service (suporte alargado). 

> [!IMPORTANT]
> Os Serviços cloud (suporte alargado) estão atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

1. Reveja os [pré-requisitos](deploy-prerequisite.md) de implantação para serviços em nuvem (suporte alargado) e crie os recursos associados. 

2. Inicie sessão no [portal do Azure](https://portal.azure.com)

3.  Utilizando a barra de pesquisa localizada no topo do portal Azure, procure e selecione **Serviços cloud (suporte alargado)**.

    :::image type="content" source="media/deploy-portal-1.png" alt-text="A imagem mostra toda a lâmina de recursos no portal Azure.":::
 
4.  No painel de serviços de nuvem (suporte alargado) **selecione Criar**. 

    :::image type="content" source="media/deploy-portal-2.png" alt-text="A imagem mostra a compra de um serviço de nuvem do mercado.":::

5. A janela de criação dos Serviços cloud (suporte alargado) será aberta ao **separador Básicos.** 
    - Selecione uma subscrição.
    - Escolha um grupo de recursos ou crie um novo.
    - Introduza o nome pretendido para a sua implementação cloud service (suporte alargado).
        - O nome DNS do serviço em nuvem é separado e especificado pela etiqueta de nome DNS do endereço IP público e pode ser modificado na secção IP pública no separador de configuração.
    -  Selecione a região para.

    :::image type="content" source="media/deploy-portal-3.png" alt-text="A imagem mostra a lâmina caseira cloud Services (suporte alargado).":::

6. Adicione a configuração do serviço de nuvem, os ficheiros de pacote e definição. Pode adicionar ficheiros existentes a partir do armazenamento de bolhas ou carregá-los a partir da sua máquina local. Se o upload da sua máquina local, estes serão armazenados numa conta de armazenamento. 

    :::image type="content" source="media/deploy-portal-4.png" alt-text="A imagem mostra a secção de upload do separador básico durante a criação.":::

7. Uma vez concluídos todos os campos, mova-se para e preencha o **separador Configuração.** 
    - Selecione uma rede virtual para associar ao Serviço cloud ou criar uma nova. 
        - As implementações do Cloud Service (suporte alargado) **devem** estar numa rede virtual. A rede virtual também **deve** ser referenciada no ficheiro De Configuração de Serviço (.cscfg) na `NetworkConfiguration` secção.
    - Selecione um endereço IP público existente para associar ao Serviço de Cloud ou criar um novo.
        - Se tiver **pontos finais de entrada IP definidos** no seu ficheiro Definição de Serviço (.csdef), terá de ser criado um endereço IP público para o seu Serviço cloud. 
        - Os Serviços cloud (suporte alargado) só suportam o endereço IP Básico SKU.
        - Se a sua Configuração de Serviço (.cscfg) contiver um endereço IP reservado, o tipo de atribuição para o IP público deve ser definido tp **Estática**. 
        - Opcionalmente, atribua um nome DNS para o seu ponto final de serviço na nuvem atualizando a propriedade da etiqueta DNS do endereço IP público que está associado ao serviço de cloud.  
    - (Opcional) Iniciar o Serviço cloud. Escolha iniciar ou não iniciar o serviço imediatamente após a criação.
    - Selecione um cofre de chaves 
        - O Cofre de Chaves é necessário quando especificar um ou mais certificados no seu ficheiro de Configuração de Serviço (.cscfg). Quando selecionar um cofre de chaves, tentaremos encontrar os certificados selecionados a partir do seu ficheiro de Configuração de Serviço (.cscfg) com base nas suas impressões digitais. Se faltar algum certificado no seu cofre de chaves, pode carregá-los agora e clicar em **Refresh**.   

 :::image type="content" source="media/deploy-portal-5.png" alt-text="A imagem mostra a lâmina de configuração no portal Azure ao criar um Cloud Services (suporte alargado).":::

8. Uma vez concluídos todos os campos, mude para o **separador 'Rever e Criar'** para validar a sua configuração de implementação e criar o seu Serviço cloud (suporte alargado).

## <a name="next-steps"></a>Próximos passos 
- Reveja [perguntas frequentes](faq.md) para serviços cloud (suporte alargado).
- Implementar um Serviço de Cloud (suporte alargado) utilizando o [portal Azure](deploy-portal.md), [PowerShell,](deploy-powershell.md) [Modelo](deploy-template.md) ou [Estúdio Visual](deploy-visual-studio.md).