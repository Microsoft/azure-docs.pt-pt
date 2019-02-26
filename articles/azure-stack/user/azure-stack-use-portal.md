---
title: Com o portal do Azure Stack | Documentos da Microsoft
description: Saiba como aceder e utilizar o portal de utilizador no Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/25/2019
ms.author: mabrigg
ms.reviewer: efemmano
ms.lastreviewed: 01/25/2019
ms.openlocfilehash: 28b8971b61ff09346b2a2f89cd5ea8ae04e44079
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56819365"
---
# <a name="use-the-azure-stack-portal"></a>Utilizar o portal do Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Pode utilizar o portal do Azure Stack para subscrever ofertas públicas e utilizar os serviços que fornecem estas ofertas. Se já utilizou o portal do Azure global, já está familiarizado com o funcionamento do site.

## <a name="access-the-portal"></a>Aceder ao portal

O operador do Azure Stack (um fornecedor de serviços ou um administrador na sua organização), permitirá que sabe o URL correto para aceder ao portal.

- Para um sistema integrado, o URL varia com base na região e o nome de domínio externo de sua operador e estará no formato https://portal.&lt; *região*&gt;.&lt; *FQDN*&gt;.
- Se estiver usando o Kit de desenvolvimento do Azure Stack, o endereço de portal é https://portal.local.azurestack.external.
- O fuso horário predefinido para todas as implementações do Azure Stack está definido para Hora Universal Coordenada (UTC). Pode selecionar um fuso horário ao instalar o Azure Stack, no entanto, ele é automaticamente revertida para UTC como predefinição durante a instalação.

## <a name="customize-the-dashboard"></a>Personalizar o dashboard

O dashboard contém um conjunto predefinido de mosaicos. Pode selecionar **editar dashboard** para modificar o dashboard predefinido ou selecione **novo dashboard** para criar um dashboard personalizado. Pode personalizar facilmente um dashboard ao adicionar ou remover mosaicos. Por exemplo, para adicionar um mosaico de computação, selecione **+ criar um recurso**. Com o botão direito **computação**e, em seguida, selecione **afixar ao dashboard**.

![Captura de ecrã do portal de utilizador do Azure Stack](media/azure-stack-use-portal/userportal.png)

Para restaurar o dashboard para as definições originais:
1.  Selecione **editar Dashboard**. 
2.  Com o botão direito e selecione **repor para o estado predefinido**.

## <a name="create-subscription-and-browse-available-resources"></a>Criar a subscrição e a procurar recursos disponíveis

Se ainda não tiver uma subscrição, a primeira coisa que precisa fazer é subscrever uma oferta. Depois disso, pode procurar os recursos disponíveis. Para procurar e criar recursos, utilize qualquer uma das seguintes abordagens:

- Selecione o **Marketplace** mosaico no dashboard.
- Sobre o **todos os recursos** mosaico, selecione **criar recursos**.
- No painel de navegação esquerdo, selecione **+ criar um recurso**.

## <a name="learn-how-to-use-available-services"></a>Saiba como utilizar os serviços disponíveis

Se precisar de orientações sobre como utilizar os serviços disponíveis, pode haver diferentes opções disponíveis para si.

- A sua organização ou fornecedor de serviços pode fornecer sua própria documentação, o que é normalmente o caso, se eles oferecem aplicações ou serviços personalizados.
- Aplicações de terceiros têm sua própria documentação.
- Para os serviços consistentes do Azure, recomendamos vivamente que reveja primeiro a documentação do Azure Stack. Para acessar a documentação do utilizador do Azure Stack, selecione o ícone de ajuda e, em seguida, selecione **ajuda + suporte**.

    ![Ajuda e suporte a opção na interface de Usuário](media/azure-stack-use-portal/HelpAndSupport.png)

    Em particular, sugerimos que reveja os artigos seguintes para começar a utilizar:

    - [Considerações-chave: Utilizar os serviços ou criar aplicativos para o Azure Stack](azure-stack-considerations.md)
    - Na **utilizar os serviços** secção da documentação, há um artigo de considerações para cada serviço. A página de considerações descreve as diferenças entre o serviço oferecidos no Azure e o mesmo serviço oferecidos no Azure Stack. Por exemplo, veja [considerações sobre VM](azure-stack-vm-considerations.md). Poderão existir outras informações no **utilizar os serviços** secção que seja exclusiva para o Azure Stack.

      Pode utilizar a documentação do Azure como referência geral para um serviço, mas deve ficar atento essas diferenças. Compreender o que se ligue a documentação sobre o **tutoriais de início rápido** mosaico ponto a documentação do Azure.

## <a name="get-support"></a>Obter suporte

Se precisar de suporte, contacte o fornecedor de serviço ou de organização para obter ajuda.

Se estiver usando o Kit de desenvolvimento do Azure Stack, o [fórum do Azure Stack](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack) é a única fonte de suporte.

## <a name="next-steps"></a>Passos Seguintes

[Considerações-chave: Utilizar os serviços ou criar aplicativos para o Azure Stack](azure-stack-considerations.md)
