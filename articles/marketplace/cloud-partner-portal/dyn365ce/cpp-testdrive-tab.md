---
title: Dynamics 365 para a aplicação de Customer Engagement oferece separador de Test-Drive - Azure Marketplace | Documentos da Microsoft
description: Como configurar a versão de teste para um de Dynamics 365 para a oferta de aplicação de envolvimento do cliente no Marketplace do AppSource.
services: Azure, Marketplace, AppSource, Cloud Partner Portal, Dynamics 365 for Customer Engagement
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 12/25/2018
ms.author: pbutlerm
ms.openlocfilehash: 373312b4c7f05fe41c9ca8165b8ff6f1b0e56f1b
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57454974"
---
# <a name="dynamics-365-for-customer-engagement-application-test-drive-tab"></a>Dynamics 365 para o separador de Test-Drive do aplicativo de Customer Engagement

Utilize o **Test-Drive** separador para criar uma experiência de avaliação para os seus clientes.  Fornece aos clientes com uma avaliação autodidata prática dos principais recursos e benefícios, demonstrados num cenário de implementação em tempo real da sua oferta.  As opções de avaliação disponíveis, Test-Drive é leva a mais uma forma eficaz de geração de alta qualidade e leva de conversão maior deles.  Para obter mais informações, consulte [o que é o Test Drive?](../test-drive/what-is-test-drive.md)

A experiência de versão de teste para aplicações do Dynamics 365 será executado automaticamente como uma solução hospedada pela Microsoft.  Para obter mais informações, consulte [alojado Test Drive](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/hosted-test-drive).

A guia de Test-Drive tem três secções possíveis: **O Test Drive**, **detalhes**, e **configuração técnica**.  As últimas duas secções são apresentadas apenas depois de ativar a funcionalidade de teste de unidade.  Um asterisco (*) acrescentado ao nome do campo indica que é obrigatório. 


## <a name="test-drive-section"></a>Secção de unidade de teste

Para ativar esta funcionalidade, selecione **Sim** ao **ativar um Test Drive**.


## <a name="details-section"></a>Secção de detalhes

Irá fornecer informações básicas da versão de teste nos **detalhes** secção.   

![Secção de detalhes da unidade de teste](./media/test-drive-tab-details.png)

A tabela seguinte descreve os campos necessários para configurar a versão de teste para a sua aplicação do Dynamics 365.

|      Campo                    |    Descrição                  |
|    ---------                  |  ---------------                |
|      Descrição              |   Descreva o que pode ser feito em sua versão de teste. Pode utilizar tags de HTML básicos para formatar a descrição. Por exemplo, &lt;p&gt;, &lt;em&gt;, &lt;ul&gt;, &lt;li&gt;, &lt;ol&gt;e cabeçalhos.  |
|  Manual do usuário                  |   Carregar um manual do usuário do que seus clientes podem utilizar para percorrer a experiência de versão de teste. Este documento tem de ser um ficheiro. pdf.              |
|  Teste de unidade vídeo de demonstração (opcional) |  Pode fornecer um vídeo com instruções sobre sua versão de teste. Um cliente pode ver este vídeo antes de uma versão de teste. Forneça um URL para o vídeo no YouTube ou Vimeo. Se selecionou **+ adicionar vídeo**, será solicitado a fornecer as seguintes informações:<ul><li>Name</li><li>do IdP</li><li>Miniatura (no formato PNG, 533 x 324 pixels)</li></ul>  |
|   |   |


## <a name="technical-configuration-section"></a>Seção de configuração do técnica

nesta secção, irá fornecer detalhes técnicos sobre a sua versão de teste.

![Secção de detalhes da unidade de teste](./media/test-drive-tab-tech-config.png)

Em que os campos têm os seguintes fins:

|      Campo                    |    Descrição                  |
|    ---------                  |  ---------------                |
| Tipo de versão de teste            | Escolher **(Dynamics 365 for Customer Engagement) hospedado da Microsoft**.  |
| Versões de teste de simultâneas máx.    | Número de instâncias simultâneas de uma unidade de teste ativa num determinado período de tempo. Cada utilizador irá consumir uma licença do Dynamics, enquanto a unidade de teste está ativa, por isso terá de garantir que tenha, pelo menos, tantos Dynamics licenças disponíveis para os utilizadores da versão de teste. Valor recomendado de 3 a 5.  |
| Teste de unidade de duração (horas)   | Número máximo de horas de que instância de Test-Drive do utilizador estará ativa para. Após este período for excedido, irá ser desaprovisionada a instância do seu inquilino. Valor de 2 a 24 horas, consoante a complexidade da sua aplicação de recomendado. O usuário sempre pode pedir outro teste se ficar sem tempo e pretende avaliar novamente.  |
| URL da instância                  | URL que o Test-Drive inicialmente irá navegar para. Isso normalmente é o URL da sua instância do Dynamics 365 que tenha a sua aplicação e dados de exemplo instalados no.  |
| ID de inquilino do Azure AD            | GUID do inquilino do Azure para a sua instância do Dynamics 365. Para obter este valor, inicie sessão no portal do Azure e navegue para **do Azure Active Directory** > **selecione propriedades** > **copiar o ID de diretório**.  |
| ID de aplicação do Azure AD               | GUID da aplicação do Azure AD  |
| Chave da aplicação do Azure AD              | Segredo da aplicação do Azure AD, por exemplo: `IJUgaIOfq9b9LbUjeQmzNBW4VGn6grr1l/n3aMrnfdk=` |
| Nome de inquilino do Azure AD          | Nome do inquilino do Azure para a instância do Dynamics 365. Utilize o formato < tenantname. > onmicrosoft.com, por exemplo: `testdrive.onmicrosoft.com`  |
| URL da Web API de instância          | URL da Web API para a sua instância do Dynamics 365. Pode obter este valor ao iniciar sessão na sua instância do Microsoft Dynamics 365 e navegar até **configurações** > **personalização** > **Developer Recursos** > **Web API (copiar esta URL) da instância**. Valor de exemplo: `https://testdrive.crm.dynamics.com/api/data/v9.0`  |
| Nome da função                     | Nome da função de segurança do Dynamics 365 personalizada que criou para a sua versão de teste e será atribuído aos utilizadores quando executam, por exemplo `testdriveuser`. |
|  |  |

Depois de fornecer todas as informações necessárias, selecione **guardar**.


## <a name="next-steps"></a>Passos Seguintes

Em seguida irá fornecer informações de marketing e vendas no [separador de detalhes de loja](./cpp-storefront-details-tab.md).

