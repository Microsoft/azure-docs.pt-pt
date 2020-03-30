---
title: Azure Application oferece test drive [ unidade de teste ] Mercado Azure
description: Como configurar o test drive para a oferta da Aplicação Azure no Mercado Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: dsindona
ms.openlocfilehash: 2f35dd69c1f29350049ed8f62dd9cbb0e60a7718
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80289010"
---
# <a name="azure-applications-test-drive-tab"></a>Separador de test drive de aplicações Azure

Utilize o separador Test Drive para proporcionar uma experiência experimental para os seus clientes.

## <a name="test-drive-benefits"></a>Benefícios de Test Drive

Criar uma experiência experimental para os seus clientes é uma boa prática para garantir que eles podem comprar com confiança. Das opções de teste disponíveis, o Test Drive é o mais eficaz na geração de leads de alta qualidade e no aumento da conversão desses leads.

Proporciona aos clientes um teste prático e auto-guiado das principais funcionalidades e benefícios do seu produto, demonstradonum cenário de implementação no mundo real.

## <a name="how-a-test-drive-works"></a>Como funciona um test drive

Um potencial cliente procura e descobre a sua aplicação no Mercado. O cliente entra e concorda com os termos de utilização. Neste ponto, o cliente recebe o seu ambiente pré-configurado para tentar um número fixo de horas, enquanto recebe uma pista altamente qualificada para acompanhar. Para mais informações, consulte [o que é test drive?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

## <a name="setting-up-a-test-drive"></a>Configuração de um test drive

Utilize os seguintes passos para ativar e configurar um test drive.

### <a name="to-enable-a-test-drive"></a>Para ativar uma unidade de teste:

1. Em **New Offer,** selecione o separador **Test Drive.**
2. Em **test drive,** selecione **Sim** para **ativar um test drive**.

   ![Ativar um test drive](./media/managed-app-enable-testdrive.png)

### <a name="to-configure-a-test-drive"></a>Para configurar uma unidade de teste:

Depois de ativar um test drive, preencherá os seguintes formulários para configurar o test drive:
  
 - Detalhes
 - Configuração Técnica
 - Detalhes da subscrição de implementação de unidade de teste

A próxima captura do ecrã mostra todos os formulários test drive. Um asterisco (*) anexado ao nome de campo indica que é necessário. 

![Configure um test drive](./media/managed-app-configure-testdrive.png)

A tabela seguinte descreve os campos necessários para configurar o test drive para a sua aplicação gerida.  Os campos anexados com um asterisco são necessários.

|      Campo         |  Descrição      |
|  ---------------   |  ---------------  |
| **Descrição\***  |  Descreva o que pode ser feito no seu Test Drive. Pode utilizar tags HTML básicas para formatar esta descrição. Por &lt;exemplo,&gt; &lt;p&gt; &lt;,&gt; &lt;em&gt; &lt;,&gt;ul, li, ol , e headings.                |
| **Manual do utilizador\***  |  Faça upload de um manual de utilizador que os seus clientes podem usar para passar pela experiência Test Drive. Este documento deve ser um ficheiro .pdf.    |
| **Vídeo de demonstração de test drive** |  Um vídeo opcional do seu Test Drive. Um cliente pode ver este vídeo antes de fazer um test drive. Forneça um URL para o vídeo no YouTube ou Vimeo. Se selecionar **+ Adicionar Vídeo,** será solicitado a fornecer as seguintes informações:<ul><li>Nome</li><li>do IdP</li><li>Miniatura (em formato PNG, 533 x 324 pixels)</li></ul>  |
| **de Instâncias\***      | Configure quantas instâncias deseja, em que região e a rapidez com que os seus clientes podem obter o Test Drive. Para mais informações, consulte [Como publicar um Test Drive](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive#how-to-publish-a-test-drive).           |
| **Duração do test drive (horas)\*** | Insira uma inteiro pelo número de horas. O intervalo permitido é de 1 a 999. |
| **Modelo de braço de acionamento de teste\***     | Faça upload de um ficheiro comprimido (.zip) que tem os seus Modelos de Gestor de Recursos Azure para a sua aplicação. Para mais informações, consulte [o Azure Resource Manager Test Drive](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive). |
| **Informação de acesso\***          | Forneça informações de acesso após o seu cliente obter o Test Drive. Por exemplo, um URL para aceder ao test drive e assinar informações. . Pode utilizar tags HTML básicas para formatar esta descrição. Por &lt;exemplo,&gt; &lt;p&gt; &lt;,&gt; &lt;em&gt; &lt;,&gt;ul, li, ol , e headings. |
| **ID de Subscrição do Azure\***       | Isto dá acesso aos serviços Azure e ao portal Azure. A subscrição é onde o uso de recursos é reportado, e os serviços são cobrados. Se ainda não tiver uma subscrição Azure separada apenas para Test Drives, crie uma subscrição.  |
| **Id de inquilino azure AD\***          | Forneça um Inquilino existente no Diretório Ativo Azure ou crie um inquilino para este test drive.  |
| **Azure AD App Id\***             | Criar e registar uma nova aplicação. A Microsoft utiliza esta aplicação para realizar operações na sua instância Test Drive.  |
| **Chave da aplicação Azure AD\***            | Crie uma chave de autenticação para a app e cola-a neste campo.   |
|  |  |

Depois de fornecer todas as informações necessárias, selecione **Save** para terminar a configuração do test drive.


## <a name="next-steps"></a>Passos seguintes

[Separador Marketplace](./cpp-marketplace-tab.md)
