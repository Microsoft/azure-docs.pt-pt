---
title: Azure Lab Services - criar informações de suporte (dono de conta de laboratório)
description: Descreve como um proprietário de uma conta de laboratório pode definir informações de contacto de suporte. Criadores de laboratório e utilizadores de laboratório podem vê-lo e usá-lo para obter ajuda.
author: spelluru
ms.author: spelluru
ms.date: 02/14/2020
ms.topic: how-to
ms.service: lab-services
ms.openlocfilehash: f387e6b8832d8e7144b8338a3fc887b6b27db789
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80109345"
---
# <a name="set-up-support-information-lab-account-owner-in-azure-lab-services"></a>Configurar informações de suporte (dono de conta de laboratório em Serviços de Laboratório Azure)
Este artigo explica como você (como proprietário de uma conta de laboratório) pode especificar informações de suporte que os criadores de laboratório (instrutores) e utilizadores de laboratório (estudantes) podem usar para obter ajuda se encontrarem problemas técnicos com a utilização da conta de laboratório ou laboratório. 

A informação de apoio inclui:

- do IdP
- Email
- Telefone
- Instruções adicionais 

## <a name="specify-support-information"></a>Especificar informações de suporte
1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Na barra de pesquisa, insira os **Serviços lab,** e selecione **Lab Services** nos resultados da pesquisa. 
3. Selecione a sua conta de laboratório na lista de contas de laboratório. 
4. Mude para a página de **suporte interno,** faça os seguintes passos:
    1. Introduza o URL de **suporte**. 
     2. Insira o **e-mail**de suporte . 
     3. Introduza o telefone de **apoio**.
     4. Introduza instruções de **apoio detalhadas** (opcional). Os proprietários e utilizadores do laboratório verão este texto juntamente com as informações de contacto de suporte. Os URLs serão automaticamente transformados em links. 
     5. Selecione **Guardar** na barra de ferramentas.

         ![Página de apoio interno](../media/lab-account-owner-support-information/internal-support-page.png)     


## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos:

- [Ver informações de contacto (criador de laboratório)](lab-creator-support-information.md)
- [Ver informações de contacto (utilizador de laboratório)](lab-user-support-information.md)
