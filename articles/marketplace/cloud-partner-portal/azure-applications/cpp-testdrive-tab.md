---
title: Versão de teste da oferta de aplicação do Azure | Documentos da Microsoft
description: Como configurar a versão de teste para a oferta de aplicação do Azure no Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: pbutlerm
ms.openlocfilehash: d51c7330acd37d8a861c136ea85a2ff456eb264e
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57446491"
---
# <a name="azure-applications-test-drive-tab"></a>Separador de versão de teste de aplicações do Azure

Utilize o separador de Test-Drive para fornecer uma experiência de avaliação para os seus clientes.

## <a name="test-drive-benefits"></a>Benefícios da unidade de teste

A criação de uma experiência de avaliação para os seus clientes é uma prática recomendada, certifique-se de que podem comprar com confiança. As opções de avaliação disponíveis, Test-Drive é leva a mais uma forma eficaz de geração de alta qualidade e leva de conversão maior deles.

Fornece aos clientes com uma avaliação autodidata prática dos principais recursos e benefícios, demonstrados num cenário de implementação em tempo real de seu produto.

## <a name="how-a-test-drive-works"></a>Como funciona um test drive

Um cliente potencial pesquisa e descobre a sua aplicação no Marketplace. O cliente inicia sessão e se concorda com os termos de utilização. Neste momento, o cliente recebe o seu ambiente previamente configurada para experimentar para um número fixo de horas, enquanto recebe uma oportunidade potencial altamente qualificada para dar seguimento com. Para obter mais informações, consulte [o que é o Test Drive?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

## <a name="setting-up-a-test-drive"></a>Configuração de uma versão de teste

Utilize os seguintes passos para ativar e configurar uma versão de teste.

### <a name="to-enable-a-test-drive"></a>Para ativar uma versão de teste:

1. Sob **nova oferta**, selecione a **Test-Drive** separador.
2. Sob **Test-Drive**, selecione **Sim** para **ativar um Test Drive**.

   ![Ativar uma versão de teste](./media/managed-app-enable-testdrive.png)

### <a name="to-configure-a-test-drive"></a>Para configurar uma versão de teste:

Depois de ativar uma versão de teste, irá preencher os formulários seguintes para configurar a versão de teste:
  
 - Detalhes
 - Configuração técnica
 - Detalhes de subscrição de implementação de unidade de teste

A seguinte captura de ecrã mostra todos os formulários de versão de teste. Um asterisco (*) acrescentado ao nome do campo indica que é obrigatório. 

![Configurar um test drive](./media/managed-app-configure-testdrive.png)

A tabela seguinte descreve os campos necessários para configurar a versão de teste para seu aplicativo gerenciado.

|    **Campo**       |  **Descrição**  |
|  ---------------   |  ---------------  |
|      Descrição              |   Descreva o que pode ser feito em sua versão de teste. Pode utilizar tags de HTML básicos para formatar a descrição. Por exemplo, &lt;p&gt;, &lt;em&gt;, &lt;ul&gt;, &lt;li&gt;, &lt;ol&gt;e cabeçalhos.                |
|    Manual do usuário                |     Carregar um manual do usuário do que seus clientes podem utilizar para percorrer a experiência de versão de teste. Este documento tem de ser um ficheiro. pdf.              |
|         Teste de unidade vídeo de demonstração (opcional)           |       Pode fornecer um vídeo com instruções sobre sua versão de teste. Um cliente pode ver este vídeo antes de uma versão de teste. Forneça um URL para o vídeo no YouTube ou Vimeo. Se selecionou **+ adicionar vídeo**, será solicitado a fornecer as seguintes informações:<ul><li>Name</li><li>do IdP</li><li>Miniatura (no formato PNG, 533 x 324 pixels)</li></ul>            |
|       Instâncias             |        Configurar o número de instâncias que pretende, em que regiões e com que velocidade seus clientes podem obter a versão de teste. Para obter mais informações, consulte [como publicar um Test Drive](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive#how-to-publish-a-test-drive).           |
|       Teste de unidade de duração (horas)             |       Introduza um número inteiro para o número de horas. O intervalo permitido é entre 1 e 999.            |
|        Modelo ARM de unidade de teste            |        Carrega um ficheiro comprimido (. zip) com modelos do Resource Manager do Azure para a sua aplicação. Para obter mais informações, consulte [versão de teste do Azure Resource Manager](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive).            |
|        Informações de acesso            |         Fornece informações de acesso após o seu cliente obtém a versão de teste. Por exemplo, um URL para acessar a unidade de teste e inscrever-se de informações. . Pode utilizar tags de HTML básicos para formatar a descrição. Por exemplo, &lt;p&gt;, &lt;em&gt;, &lt;ul&gt;, &lt;li&gt;, &lt;ol&gt;e cabeçalhos.          |
|       Id de subscrição de acesso             |       Isto concede acesso a serviços do Azure e o portal do Azure. A subscrição está em que é comunicada a utilização de recursos e serviços são cobrados. Se ainda não tiver uma subscrição separada do Azure para versões de teste apenas, crie uma subscrição.             |
|          Id de inquilino do Azure AD          |        Forneça um inquilino existente no Azure Active Directory ou criar um inquilino para esta versão de teste.           |
|         Id de aplicação do Azure AD           |       Criar e registar uma nova aplicação. A Microsoft utiliza esta aplicação para realizar operações na sua instância de teste de unidade.            |
|          Chave da aplicação do Azure AD          |         Crie uma chave de autenticação para a aplicação e colá-lo neste campo.          |

Depois de fornecer todas as informações necessárias, selecione **guardar** para concluir a configuração a versão de teste.

## <a name="next-steps"></a>Passos Seguintes

[Separador Marketplace](./cpp-marketplace-tab.md)