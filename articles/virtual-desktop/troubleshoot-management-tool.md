---
title: Ferramenta de gestão de ambiente de trabalho virtual Windows - Azure
description: Como resolver problemas com a ferramenta de gestão do Ambiente de Trabalho Virtual do Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 11/09/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 9d07ba42e83d9eec071ab047e9e1e92bac1f1411
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2020
ms.locfileid: "79127481"
---
# <a name="troubleshoot-the-windows-virtual-desktop-management-tool"></a>Resolução de problemas da ferramenta de gestão do Windows Virtual Desktop

Este artigo descreve problemas que podem ocorrer ao implementar a ferramenta de gestão do Ambiente de Trabalho Virtual do Windows e como corrigi-los.

## <a name="error-management-tool-services-configured-but-automated-setup-fails"></a>Erro: Serviços de ferramentas de gestão configurados mas configuração automatizada falha

Quando configurar com sucesso os serviços para a ferramenta de gestão, mas a configuração automatizada falha, verá esta mensagem de erro:

```console
{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Conflict","message":"{\r\n "status": "Failed",\r\n "error": {\r\n "code": "ResourceDeploymentFailure",\r\n "message": "The resource operation completed with terminal provisioning state 'Failed'."\r\n }\r\n}"}]}
```

Isto geralmente significa uma das duas coisas seguintes:

- O utilizador tem permissões de proprietários na sua subscrição e administração global a nível de inquilinos, mas não podem assinar no Azure.
- As definições de conta do utilizador têm a autenticação multifactor ativada.

Para corrigir isto:

1. Certifique-se de que o utilizador que criou para o Nome Principal do Utilizador do Diretório Ativo Azure tem o nível de subscrição "Contributor".
2. Inscreva-se em <portal.azure.com> com a conta UPN para verificar as definições da conta e certificar-se de que a autenticação de vários fatores não está em vigor. Se estiver ligado, desligue.
3. Visite a página de Consentimento virtual do Windows e certifique-se de que as aplicações do servidor e do cliente têm consentimento.
4. Reveja o tutorial de [ferramentas de gestão](manage-resources-using-ui.md) se o problema continuar e recolocar a ferramenta.

## <a name="error-job-with-specified-id-already-exists"></a>Erro: Já existe trabalho com ID especificado

Se o utilizador vir a mensagem de erro "Job with specific ID already existy", é porque não forneceu um nome único no parâmetro "Nome de aplicação" ao implementar o modelo.

Para corrigir isto, reimplante a ferramenta de gestão com o parâmetro "Nome de aplicação" preenchido.

## <a name="delayed-consent-prompt-when-opening-management-tool"></a>Pedido de consentimento atrasado ao abrir ferramenta de gestão

Quando implementar a ferramenta de gestão, o pedido de consentimento pode não ser aberto imediatamente. Isto significa que o serviço de aplicações Azure Web está a demorar mais tempo do que o habitual para carregar. A solicitação deve aparecer depois de o Azure Web estar a fazer o carregamento.

## <a name="the-user-cant-deploy-the-management-tool-in-the-east-us-region"></a>O utilizador não pode implementar a ferramenta de gestão na região leste dos EUA

Se um cliente definir a região para os EUA Orientais, não pode implementar a ferramenta de gestão.

Para corrigir isto, implemente a ferramenta de gestão numa região diferente. A recolocação da ferramenta numa região diferente não deve afetar a experiência do utilizador.

## <a name="next-steps"></a>Passos seguintes

- Aprenda sobre faixas de escalada na [visão geral, feedback e suporte](troubleshoot-set-up-overview.md)de resolução de problemas.
- Saiba como relatar problemas com ferramentas de ambiente de trabalho virtual do Windows nos [modelos ARM para serviços de ambiente](https://github.com/Azure/RDS-Templates/blob/master/README.md)de trabalho remotos .
- Para uma visão geral sobre a resolução de problemas do Windows Virtual Desktop e as faixas de escalada, consulte a [visão geral, feedback e suporte](troubleshoot-set-up-overview.md)de Resolução de Problemas.
- Para aprender a implementar a ferramenta de gestão, consulte [implementar uma ferramenta de gestão](manage-resources-using-ui.md).