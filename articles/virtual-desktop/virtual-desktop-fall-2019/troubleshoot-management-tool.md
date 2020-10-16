---
title: Ferramenta de gestão virtual do Windows Desktop (clássico) - Azure
description: Como resolver problemas com a ferramenta de gestão virtual do Windows Desktop (clássico).
author: Heidilohr
ms.topic: troubleshooting
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 6f03284103b4a2aa6900bf1ba5c50a4688c50b0e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88005487"
---
# <a name="troubleshoot-the-windows-virtual-desktop-classic-management-tool"></a>Resolução de problemas da ferramenta de gestão virtual do Windows Desktop (clássico)

>[!IMPORTANT]
>Este conteúdo aplica-se ao Windows Virtual Desktop (clássico), que não suporta objetos de desktop virtual do Gestor de Recursos Azure Windows.

Este artigo descreve problemas que podem ocorrer durante a implementação da ferramenta de gestão virtual do Windows Desktop e como corrigi-los.

## <a name="error-management-tool-services-configured-but-automated-setup-fails"></a>Erro: Serviços de ferramentas de gestão configurados mas falha na configuração automatizada

Quando configurar com sucesso os serviços para a ferramenta de gestão, mas a configuração automatizada falhar, verá esta mensagem de erro:

```console
{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Conflict","message":"{\r\n "status": "Failed",\r\n "error": {\r\n "code": "ResourceDeploymentFailure",\r\n "message": "The resource operation completed with terminal provisioning state 'Failed'."\r\n }\r\n}"}]}
```

Isto geralmente significa uma das duas coisas seguintes:

- O utilizador tem permissões de proprietário na sua subscrição e administração global a nível de inquilinos, mas não pode entrar no Azure.
- As definições da conta do utilizador têm a autenticação de vários fatores ativada.

Para corrigir isto:

1. Certifique-se de que o utilizador criado para o Nome Principal do Utilizador do Diretório Ativo Azure tem o nível de subscrição "Contribuinte".
2. Faça o sômis para <portal.azure.com> com a conta UPN para verificar as definições da conta e certifique-se de que a autenticação de vários fatores não está acesa. Se estiver ligado, desligue-o.
3. Visite a página de Consentimento Virtual do Windows desktop e certifique-se de que o servidor e as aplicações do cliente têm consentimento.
4. Reveja o tutorial [de ferramentas de gestão](manage-resources-using-ui.md) se o problema continuar e recolocar a ferramenta.

## <a name="error-job-with-specified-id-already-exists"></a>Erro: Já existe trabalho com ID especificado

Se o utilizador vê a mensagem de erro "Job with specific ID já existe", é porque não forneceu um nome único no parâmetro "Nome de aplicação" ao implementar o modelo.

Para corrigir isto, reimplante a ferramenta de gestão com o parâmetro "Nome de aplicação" preenchido.

## <a name="delayed-consent-prompt-when-opening-management-tool"></a>Pedido de consentimento atrasado ao abrir a ferramenta de gestão

Quando implementar a ferramenta de gestão, o pedido de consentimento pode não abrir imediatamente. Isto significa que o serviço de aplicações Azure Web está a demorar mais tempo do que o habitual para carregar. A indicação deve aparecer depois de a Azure Web estar pronta a carregar.

## <a name="the-user-cant-deploy-the-management-tool-in-the-east-us-region"></a>O utilizador não pode implementar a ferramenta de gestão na região leste dos EUA

Se um cliente define a região para os EUA, não pode implementar a ferramenta de gestão.

Para corrigir isto, desloque a ferramenta de gestão numa região diferente. A recolocação da ferramenta numa região diferente não deve afetar a experiência do utilizador.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre as faixas de escalada na [visão geral de resolução de problemas, feedback e suporte](troubleshoot-set-up-overview-2019.md).
- Saiba como reportar problemas com ferramentas de ambiente de trabalho virtual do Windows nos [modelos ARM para serviços de ambiente de trabalho remotos.](https://github.com/Azure/RDS-Templates/blob/master/README.md)
- Para aprender a implantar a ferramenta de gestão, consulte [implementar uma ferramenta de gestão.](manage-resources-using-ui.md)