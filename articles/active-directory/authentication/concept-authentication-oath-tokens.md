---
title: Método de autenticação de fichas de OATH - Diretório Ativo Azure
description: Saiba mais sobre a utilização de fichas OATH no Azure Ative Directory para ajudar a melhorar e garantir eventos de entrada
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 09/02/2020
ms.author: justinha
author: justinha
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: d04075b415bace4104a58e8221d764355d3318d0
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2020
ms.locfileid: "96744283"
---
# <a name="authentication-methods-in-azure-active-directory---oath-tokens"></a>Métodos de autenticação em Diretório Ativo Azure - Fichas de OATH

OATH TOTP (Password de Tempo Único) é um padrão aberto que especifica como os códigos de senha única (OTP) são gerados. O OATH TOTP pode ser implementado usando software ou hardware para gerar os códigos. A Azure AD não suporta o OATH HOTP, um padrão diferente de geração de código.

## <a name="oath-software-tokens"></a>Fichas de software OATH

Os tokens de OATH do software são normalmente aplicações como a app Microsoft Authenticator e outras aplicações autenticadoras. O Azure AD gera a chave secreta, ou semente, que é a entrada na app e usada para gerar cada OTP.

A aplicação Authenticator gera automaticamente códigos quando configurado para fazer notificações push para que um utilizador tenha uma cópia de segurança mesmo que o seu dispositivo não tenha conectividade. Aplicações de terceiros que usam OATH TOTP para gerar códigos também podem ser usadas.

Algumas fichas de hardware OATH TOTP são programáveis, o que significa que não vêm com uma chave secreta ou sementes pré-programadas. Estes tokens de hardware programáveis podem ser configurados usando a chave secreta ou semente obtida a partir do fluxo de configuração do token do software. Os clientes podem comprar estes tokens ao fornecedor à sua escolha e usar a chave secreta ou a semente no processo de configuração do seu fornecedor.

## <a name="oath-hardware-tokens-preview"></a>Fichas de hardware do OATH (pré-visualização)

A Azure AD suporta a utilização de fichas OATH-TOTP SHA-1 que atualizam códigos a cada 30 ou 60 segundos. Os clientes podem comprar estes tokens ao fornecedor à sua escolha.

Os tokens de hardware OATH TOTP normalmente vêm com uma chave secreta, ou sementes, pré-programadas no token. Estas teclas devem ser inseridas no AD Azure, conforme descrito nos passos seguintes. As teclas secretas estão limitadas a 128 caracteres, o que pode não ser compatível com todos os tokens. A chave secreta só pode conter os caracteres *a-z* ou *A-Z* e os dígitos *1-7*, e deve ser codificado na *Base32*.

As fichas de hardware do OATH TOTP programáveis que podem ser ressequidas também podem ser configuradas com Azure AD no fluxo de configuração do token do software.

As fichas de hardware da OATH são suportadas como parte de uma pré-visualização pública. Para obter mais informações sobre pré-visualizações, consulte  [Termos Complementares de Utilização para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

![Upload de fichas de OATH para a lâmina de fichas de OATH do MFA](media/concept-authentication-methods/mfa-server-oath-tokens-azure-ad.png)

Uma vez adquiridas as fichas, devem ser carregadas num formato de ficheiro de valores separados por vírgula (CSV), incluindo a UPN, número de série, chave secreta, intervalo de tempo, fabricante e modelo como mostrado no seguinte exemplo:

```csv
upn,serial number,secret key,time interval,manufacturer,model
Helga@contoso.com,1234567,1234567abcdef1234567abcdef,60,Contoso,HardwareKey
```

> [!NOTE]
> Certifique-se de que inclui a linha do cabeçalho no seu ficheiro CSV.

Uma vez devidamente formatado como um ficheiro CSV, um administrador pode então iniciar sedutar no portal Azure, navegar para **o Azure Ative Directory > Security > MFA > tokens OATH**, e carregar o ficheiro CSV resultante.

Dependendo do tamanho do ficheiro CSV, pode levar alguns minutos a processar. Selecione o botão **'Renovar'** para obter o estado atual. Se houver algum erro no ficheiro, pode descarregar um ficheiro CSV que enumera quaisquer erros para resolver. Os nomes de campo no ficheiro CSV descarregado são diferentes da versão carregada.

Uma vez abordados quaisquer erros, o administrador pode então ativar cada tecla selecionando **ativar** para o token e introduzindo o OTP exibido no token.

Os utilizadores podem ter uma combinação de até cinco fichas de hardware OATH ou aplicações autenticadoras, como a aplicação Microsoft Authenticator, configurada para ser utilizada a qualquer momento.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre a configuração dos métodos de autenticação utilizando a [beta API do Microsoft Graph REST](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta).
