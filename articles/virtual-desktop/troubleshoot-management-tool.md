---
title: Ferramenta de gerenciamento de área de trabalho virtual do Windows-Azure
description: Como solucionar problemas com a ferramenta de gerenciamento de área de trabalho virtual do Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 11/09/2019
ms.author: helohr
ms.openlocfilehash: dd9fd135536fc2cb222b908f3d61c1d1430e65b0
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2019
ms.locfileid: "73905618"
---
# <a name="troubleshoot-the-windows-virtual-desktop-management-tool"></a>Solucionar problemas da ferramenta de gerenciamento de área de trabalho virtual do Windows

Este artigo descreve problemas que podem ocorrer durante a implantação da ferramenta de gerenciamento de área de trabalho virtual do Windows e como corrigi-las.

## <a name="error-management-tool-services-configured-but-automated-setup-fails"></a>Erro: serviços da ferramenta de gerenciamento configurados, mas a instalação automatizada falha

Quando você configurar os serviços para a ferramenta de gerenciamento com êxito, mas a instalação automatizada falhar, você verá esta mensagem de erro:

```console
{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Conflict","message":"{\r\n "status": "Failed",\r\n "error": {\r\n "code": "ResourceDeploymentFailure",\r\n "message": "The resource operation completed with terminal provisioning state 'Failed'."\r\n }\r\n}"}]}
```

Isso geralmente significa uma das duas coisas a seguir:

- O usuário tem permissões de proprietário em sua assinatura e administrador global no nível do locatário, mas eles não podem entrar no Azure.
- As configurações de conta do usuário têm a autenticação multifator habilitada.

Para corrigir isso:

1. Verifique se o usuário criado para o Azure Active Directory nome principal do usuário tem o nível de assinatura "colaborador".
2. Entre no portal do <. Azure. com > com a conta de UPN para verificar as configurações da conta e se certificar de que a autenticação multifator não esteja ativada. Se ele estiver ativado, desative-o.
3. Visite a página de consentimento da área de trabalho virtual do Windows e verifique se os aplicativos cliente e servidor têm consentimento.
4. Examine o tutorial [implantar ferramenta de gerenciamento](manage-resources-using-ui.md) se o problema continuar e reimplantar a ferramenta.

## <a name="error-job-with-specified-id-already-exists"></a>Erro: o trabalho com a ID especificada já existe

Se o usuário vir a mensagem de erro "o trabalho com a ID especificada já existe", é porque não forneceu um nome exclusivo no parâmetro "nome do aplicativo" ao implantar o modelo.

Para corrigir isso, reimplante a ferramenta de gerenciamento com o parâmetro "nome do aplicativo" preenchido.

## <a name="delayed-consent-prompt-when-opening-management-tool"></a>Prompt de consentimento atrasado ao abrir a ferramenta de gerenciamento

Quando você implanta a ferramenta de gerenciamento, a solicitação de consentimento pode não abrir imediatamente. Isso significa que o serviço de aplicativo Web do Azure está demorando mais do que o normal para carregar. O prompt deve aparecer depois que a Web do Azure terminar de ser carregada.

## <a name="the-user-cant-deploy-the-management-tool-in-the-east-us-region"></a>O usuário não pode implantar a ferramenta de gerenciamento na região leste dos EUA

Se um cliente definir a região para o leste dos EUA, ele não poderá implantar a ferramenta de gerenciamento.

Para corrigir isso, implante a ferramenta de gerenciamento em uma região diferente. Reimplantar a ferramenta em uma região diferente não deve afetar a experiência do usuário.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre as faixas de escalonamento em [visão geral, comentários e suporte da solução de problemas](troubleshoot-set-up-overview.md).
- Saiba como relatar problemas com as ferramentas de área de trabalho virtual do Windows em [modelos ARM para serviços de área de trabalho remota](https://github.com/Azure/RDS-Templates/blob/master/README.md).
- Para obter uma visão geral da solução de problemas da área de trabalho virtual do Windows e das faixas de escalonamento, consulte [visão geral da solução de problemas, comentários e suporte](troubleshoot-set-up-overview.md).
- Para saber como implantar a ferramenta de gerenciamento, consulte [implantar uma ferramenta de gerenciamento](manage-resources-using-ui.md).