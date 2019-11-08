---
title: Aplicativo Azure oferta test drive | Azure Marketplace
description: Como configurar test drive para a oferta de Aplicativo Azure no Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: pabutler
ms.openlocfilehash: 3855c600fe35c37ac15783995551a769e00532be
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826125"
---
# <a name="azure-applications-test-drive-tab"></a>Guia unidade de teste dos aplicativos do Azure

Use a guia Test Drive para fornecer uma experiência de avaliação para seus clientes.

## <a name="test-drive-benefits"></a>Benefícios do Test Drive

A criação de uma experiência de avaliação para seus clientes é uma prática recomendada para garantir que eles possam comprar com confiança. Das opções de avaliação disponíveis, o Test Drive é o mais eficiente na geração de leads de alta qualidade e uma maior conversão desses leads.

Ele fornece aos clientes uma avaliação prática e Autoguiada dos principais recursos e benefícios do seu produto, demonstrados em um cenário de implementação do mundo real.

## <a name="how-a-test-drive-works"></a>Como funciona um test drive

Um cliente potencial pesquisa e descobre seu aplicativo no Marketplace. O cliente entra e concorda com os termos de uso. Neste ponto, o cliente recebe seu ambiente pré-configurado para tentar um número fixo de horas, enquanto você recebe um Lead altamente qualificado para acompanhar. Para obter mais informações, consulte [o que é Test Drive?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

## <a name="setting-up-a-test-drive"></a>Configurando um test drive

Use as etapas a seguir para habilitar e configurar um test drive.

### <a name="to-enable-a-test-drive"></a>Para habilitar uma test drive:

1. Em **nova oferta**, selecione a guia **Test Drive** .
2. Em **testar unidade**, selecione **Sim** para **habilitar um test drive**.

   ![Habilitar um test drive](./media/managed-app-enable-testdrive.png)

### <a name="to-configure-a-test-drive"></a>Para configurar um test drive:

Depois de habilitar uma test drive, você preencherá os seguintes formulários para configurar o test drive:
  
 - Detalhes
 - Configuração técnica
 - Detalhes da assinatura de implantação do Test Drive

A próxima captura de tela mostra todos os formulários do Test Drive. Um asterisco (*) acrescentado ao nome do campo indica que ele é necessário. 

![Configurar um test drive](./media/managed-app-configure-testdrive.png)

A tabela a seguir descreve os campos necessários para configurar o test drive para o aplicativo gerenciado.  Os campos anexados a um asterisco são obrigatórios.

|      Campo         |  Descrição      |
|  ---------------   |  ---------------  |
| **Descrição\***  |  Descreva o que pode ser feito em seu Test Drive. Você pode usar marcas HTML básicas para formatar essa descrição. Por exemplo, &lt;p&gt;, &lt;em&gt;, &lt;UL&gt;, &lt;li&gt;, &lt;ol&gt;e cabeçalhos.                |
| **\* manual do usuário**  |  Carregue um manual do usuário que seus clientes possam usar para percorrer a experiência do Test Drive. Este documento deve ser um arquivo. pdf.    |
| **Vídeo de demonstração do Test Drive** |  Uma explicação de vídeo opcional em seu Test Drive. Um cliente pode assistir a este vídeo antes que eles tenham um test drive. Forneça uma URL para o vídeo no YouTube ou no Vimeo. Se você selecionar **+ Adicionar vídeo**, será solicitado que você forneça as seguintes informações:<ul><li>Nome</li><li>do IdP</li><li>Miniatura (no formato PNG, 533 x 324 pixels)</li></ul>  |
| **Instâncias\***      | Configure quantas instâncias você deseja, em quais regiões e a rapidez com que seus clientes podem obter o Test Drive. Para obter mais informações, consulte [como publicar um test drive](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive#how-to-publish-a-test-drive).           |
| **Duração da unidade de teste (horas)\*** | Insira um número inteiro para o número de horas. O intervalo permitido é de 1 a 999. |
| **Testar o modelo ARM\***     | Carregue um arquivo compactado (. zip) que tenha seus modelos de Azure Resource Manager para seu aplicativo. Para obter mais informações, consulte [Azure Resource Manager Test Drive](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive). |
| **Informações de acesso\***          | Forneça informações de acesso após o cliente obter o Test Drive. Por exemplo, uma URL para acessar o test drive e assinar informações. . Você pode usar marcas HTML básicas para formatar essa descrição. Por exemplo, &lt;p&gt;, &lt;em&gt;, &lt;UL&gt;, &lt;li&gt;, &lt;ol&gt;e cabeçalhos. |
| **ID da assinatura do Azure\***       | Isso concede acesso aos serviços do Azure e ao portal do Azure. A assinatura é onde o uso de recursos é relatado e os serviços são cobrados. Se você ainda não tiver uma assinatura separada do Azure para apenas unidades de teste, crie uma assinatura.  |
| **ID de locatário do Azure AD\***          | Forneça um locatário existente no Azure Active Directory ou crie um locatário para este test drive.  |
| **ID de Aplicativo Azure AD\***             | Crie e registre um novo aplicativo. A Microsoft usa esse aplicativo para executar operações em sua instância de unidade de teste.  |
| **Chave de Aplicativo Azure AD\***            | Crie uma chave de autenticação para o aplicativo e cole-a nesse campo.   |
|  |  |

Depois de fornecer todas as informações necessárias, selecione **salvar** para concluir a configuração do Test Drive.


## <a name="next-steps"></a>Passos seguintes

[Separador Marketplace](./cpp-marketplace-tab.md)
