---
title: Guia de Test-Drive de máquina virtual no Portal de parceiros na Cloud para o Azure Marketplace | Documentos da Microsoft
description: Descreve a guia de Test-Drive usada na criação de uma oferta de VM do Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal, virtual machine
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 10/19/2018
ms.author: pbutlerm
ms.openlocfilehash: 97fb21dc390bd365357f6395c72aa282423c83c9
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2018
ms.locfileid: "49640013"
---
# <a name="virtual-machine-test-drive-tab"></a>Guia de Test-Drive da máquina virtual

<!-- TD: The AMP tree needs a conceptual/business overview of Test Drive. I've deleted all the marketing fluff and most of overview from this topic.   See also https://azure.microsoft.com/blog/azure-marketplace-test-drive/ and https://github.com/Azure/AzureTestDrive/wiki/What-is-a-Test-Drive. --> 

O **Test-Drive** separador da **nova oferta** página permite-lhe fornecer a seus clientes potenciais com uma demonstração prática, autodidata de seu produto principais recursos e benefícios, demonstrados num cenário padronizado.  Versão de teste é uma funcionalidade opcional para os tipos de oferta que suporta a versão de teste.  Versão de teste requer suporte ativos para ser implementada corretamente.  Para obter mais informações, consulte o artigo [do Azure Marketplace Test-Drive](https://azure.microsoft.com/blog/azure-marketplace-test-drive/).  <!--TD: Replace with migrated version of Test Drive article! -->

Para ativar esta funcionalidade, no **Test-Drive** separador, clique nas **Sim** opção **ativar um Test Drive**.  O **Test-Drive** separador apresenta os campos disponíveis para edição.  Um anexado asterisco (*) no nome do campo indica que é necessário.

![Separador de unidade de teste no formulário de nova oferta para máquinas virtuais](./media/publishvm_007.png)

<br/>

A tabela seguinte descreve a finalidade e o conteúdo desses campos.


|  **Campo**                |     **Descrição**                                                          |
|  ---------                |     ---------------                                                          |
|  *Detalhes*   |  |
| **Descrição**           | Forneça uma descrição geral do seu cenário de teste de unidade. Esse texto será apresentado ao utilizador enquanto o Test-Drive está a ser aprovisionado. Este campo suporta HTML básico para fornecer conteúdo formatado.  |
| **Manual do usuário**           | Carregar um manual de utilizador detalhadas (. pdf) que ajuda os utilizadores de Test-Drive compreender como utilizar a sua solução.  |
| **Vídeo de demonstração de unidade de teste** | Carregar um vídeo que demonstra a sua solução.  Se tiver escolhido esta opção, tem de fornecer um nome, o URL para o vídeo (alojado no YouTube ou Vimeo) e uma miniatura (533 x 324 pixel) para o vídeo. |
| *Configuração técnica* |  |
| **instâncias**             | Especifique a disponibilidade das regiões e relativamente a disponibilidade da instância de vm (clique no ícone de informações para obter mais detalhes).  <br/>Potenciais sessões simultâneas da versão de teste não devem exceder o limite de quota para a sua subscrição.  A primeira é calculada como: [número de regiões selecionadas] x [instâncias de acesso frequente] + [número de regiões selecionadas] x [instâncias quente] + [número de regiões selecionadas] x [instâncias frios] |
| **Duração do teste de unidade**   | Duração de duração máxima da sessão em horas. A sessão de teste de unidade é encerrada automaticamente após este período de tempo foi excedido.  |
|**Modelo ARM de unidade de teste**| Carregar o modelo de Gestor de recursos do Azure associado esta versão de teste. Para obter mais informações, consulte [transformar o modelo de implementação de Máquina Virtual para Test-Drive](https://github.com/Azure/AzureTestDrive/wiki/Transforming-Virtual-Machine-Deployment-Template-for-Test-Drive). |
| **Informações de acesso**    | Acesso de Gestor de recursos do Azure e as informações de início de sessão de avaliação, escrito como texto sem formatação ou simple HTML. |
| *Detalhes de subscrição de implementação de unidade de teste* |  |
| **Id de subscrição do Azure** | Pode ser obtido ao iniciar sessão a [portal do Microsoft Azure](https://ms.portal.azure.com) e clicando em **subscrições** na barra de menu à esquerda. (Exemplo: "a83645ac-1234-5ab6-6789-1h234g764ghty")    Este identificador deve ser um GUID do formulário `a83645ac-1234-5ab6-6789-1h234g764ghty`.|
| **Id de inquilino do Azure AD**    | ID do inquilino do Active Directory do Azure.  Pode ser obtido ao iniciar sessão a [portal do Microsoft Azure](https://ms.portal.azure.com) e clicando em **Azure Active Directory** na barra de menu à esquerda, em seguida, clicando em **propriedades** na barra de menus intermediária, em seguida, copiar os **ID de diretório** do formulário.  Este identificador também deve ser um GUID.  Se deixado em branco, em seguida, tem de criar um ID de inquilino para a sua organização. |
| **Id de aplicação do Azure AD**       | Identificador para a sua solução de VM do Azure registada  |
| **Chave da aplicação do Azure AD**      | Chave de autenticação para a sua solução registada |
|  |  |

<br/>

Nos próximos [Marketplace](./cpp-marketplace-tab.md) guia, irá fornecer informações legais e de marketing sobre a sua solução.
