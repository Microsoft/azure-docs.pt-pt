---
title: Inscreva-se no Azure Storage Explorer | Microsoft Docs
description: Documentação sobre a assinatura no Azure Storage Explorer
services: storage
author: MRayermannMSFT
ms.service: storage
ms.topic: article
ms.date: 04/01/2021
ms.author: chuye
ms.openlocfilehash: ef02842d189746a1801d97f91b92f249947c832d
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568728"
---
# <a name="sign-in-to-storage-explorer"></a>Inscreva-se no Storage Explorer

O s-in é a forma recomendada de aceder aos seus recursos de armazenamento Azure com o Storage Explorer. Ao assinar, aproveita as permissões apoiadas pela Azure AD, tais como RBAC e Gen2 POSIX ACLs. 

## <a name="how-to-sign-in"></a>Como iniciar a sessão

Para iniciar sação no Storage Explorer, abra o **diálogo 'Ligar'.** Pode abrir o **diálogo 'Ligar'** a partir da barra de ferramentas vertical à esquerda ou clicando na **conta Adicionar...** no **Painel de Contas**.

Assim que tiver o diálogo aberto, escolha **a Subscrição** como o tipo de recurso a que pretende ligar e clique em **Seguinte**.

Agora tens de escolher em que ambiente azure queres entrar. Você pode escolher em qualquer um dos ambientes conhecidos, como Azure ou Azure China, ou você pode adicionar seu próprio ambiente. Assim que tiver o seu ambiente selecionado, clique em **Seguinte**.

Neste momento, será lançado o **navegador web padrão** do seu SISTEMA e será aberta uma página de sposição. Para obter os melhores resultados, deixe esta janela do navegador aberta enquanto estiver a utilizar o Storage Explorer ou pelo menos até ter realizado todos os MFA esperados. Quando terminar de iniciar sessão, pode voltar ao Storage Explorer.

## <a name="managing-accounts"></a>Gerir contas

Pode gerir e remover as contas Azure em que se inscreveu no Painel de **Contas.** Pode abrir o **Painel de Contas** clicando no botão **'Gerir contas'** na barra de ferramentas vertical à esquerda.

No **Painel de Contas** verá quaisquer contas em que tenha assinado. Em cada conta estará:
- Os inquilinos a conta pertence a
- Para cada inquilino, as assinaturas a que tem acesso

Por defeito, o Storage Explorer só o inscreve no seu inquilino de casa. Se quiser ver subscrições e recursos de outro inquilino, terá de ativar esse inquilino. Para ativar um inquilino, verifique a caixa de verificação ao lado. Uma vez que você termina de trabalhar com um inquilino, você pode desmarcar a caixa de verificação para desativá-la. Não pode desativar o seu inquilino.

Depois de ativar um inquilino, poderá ter de reentrar nas suas credenciais antes que o Storage Explorer possa carregar subscrições ou aceder a recursos do arrendatário. Ter de reentrar nas suas credenciais geralmente acontece devido a uma política de acesso condicional (CA) como a autenticação de vários fatores (MFA). E mesmo que já tenha feito MFA para outro inquilino, ainda pode ter que fazê-lo novamente. Para reentrar nas suas credenciais, basta clicar nas **credenciais do Reenter...** Também pode clicar em **detalhes de Erro...** para ver exatamente por que as subscrições não foram carregadas.

Uma vez carregadas as suas subscrições, pode escolher quais as que pretende filtrar dentro/fora, verificando ou desmarcando as suas caixas de verificação.

Se quiser remover toda a sua conta Azure, clique no **Remover** ao lado da conta.

## <a name="changing-where-sign-in-happens"></a>Mudar onde o sin-in acontece

Por predefinição, o sômin terá acesso no **navegador web padrão** do seu SISTEMA. A sessão com o seu navegador web padrão simplifica a forma como acede a recursos protegidos através de políticas de CA, como o MFA. Se por alguma razão iniciar sessão com o **navegador web padrão** do seu SISTEMA não estiver a funcionar, pode alterar onde ou como o Storage Explorer realiza o sessão de sessão.

Em **Definições**  >  **Iniciars A inscrição da aplicação,** procure a  >   **sposição com** a definição. Existem três opções:
- **Web Browser predefinido**: o sedus-in acontecerá no **navegador web padrão** do seu SISTEMA. Esta opção é recomendada.
- **Entrada integrada**: o sedus-in acontecerá numa janela do Explorador de Armazenamento. Esta opção pode ser útil se estiver a tentar iniciar sessão com várias contas da Microsoft (MSAs) de uma só vez. Você pode ter problemas com algumas políticas de CA se você escolher esta opção.
- **Fluxo de código do dispositivo**: O Explorador de Armazenamento dará-lhe um código para entrar numa janela do navegador. Esta opção não é recomendada. O fluxo de código do dispositivo não é compatível com muitas políticas de CA.

## <a name="troubleshooting-sign-in-issues"></a>Resolver problemas de início de sessão

Se tiver problemas em iniciar sessão ou se tiver problemas com uma conta Azure depois de iniciar sessão, consulte o [sinal na secção do guia de resolução de problemas do Storage Explorer](./storage-explorer-troubleshooting.md#sign-in-issues).
