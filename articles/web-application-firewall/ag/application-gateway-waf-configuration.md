---
title: Limites de tamanho de solicitação de firewall do aplicativo Web e listas de exclusão no gateway Aplicativo Azure-portal do Azure
description: Este artigo fornece informações sobre limites de tamanho de solicitação de firewall do aplicativo Web e a configuração de listas de exclusão no gateway de aplicativo com o portal do Azure.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 10/17/2019
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: cfde1355ef5e5a2f9033456ac4089ce3ca3f9d72
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73839957"
---
# <a name="web-application-firewall-request-size-limits-and-exclusion-lists"></a>Limites de tamanho de solicitação de firewall do aplicativo Web e listas de exclusão

O WAF (firewall do aplicativo Web) Aplicativo Azure Gateway fornece proteção para aplicativos Web. Este artigo descreve os limites de tamanho de solicitação WAF e a configuração de listas de exclusão. Essas configurações estão localizadas na política WAF associada ao seu gateway de aplicativo. Para saber mais sobre as políticas de WAF, consulte [Firewall do aplicativo Web do Azure no gateway de aplicativo Azure](ag-overview.md) e [criar políticas de firewall do aplicativo Web para o gateway de aplicativo](create-waf-policy-ag.md)

## <a name="waf-exclusion-lists"></a>Listas de exclusão do WAF

![Limites de tamanho da solicitação](../media/application-gateway-waf-configuration/waf-policy.png)

As listas de exclusão do WAF permitem omitir determinados atributos de solicitação de uma avaliação do WAF. Um exemplo comum é Active Directory tokens inseridos que são usados para campos de autenticação ou senha. Esses atributos são propensos a conter caracteres especiais que podem disparar um falso positivo das regras de WAF. Depois que um atributo é adicionado à lista de exclusão WAF, ele não é considerado por nenhuma regra WAF configurada e ativa. As listas de exclusão são globais no escopo.

Os atributos a seguir podem ser adicionados às listas de exclusão por nome. Os valores do campo escolhido não são avaliados em relação às regras WAF, mas seus nomes ainda são (Veja o exemplo 1 abaixo, o valor do cabeçalho User-Agent é excluído da avaliação WAF). As listas de exclusão removem a inspeção do valor do campo.

* Cabeçalhos de solicitação
* Solicitar cookies
* O nome do atributo de solicitação (args) pode ser adicionado como um elemento de exclusão, como:

   * Nome do campo de formulário
   * Entidade XML
   * Entidade JSON
   * Argumentos de cadeia de caracteres de consulta de URL

Você pode especificar um cabeçalho de solicitação exato, um corpo, um cookie ou uma correspondência de atributo de cadeia de caracteres de consulta.  Ou, opcionalmente, você pode especificar correspondências parciais. As regras de exclusão são globais no escopo e se aplicam a todas as páginas e todas as regras.

A seguir estão os operadores de critérios de correspondência com suporte:

- **Equals**: esse operador é usado para uma correspondência exata. Por exemplo, para selecionar um cabeçalho chamado **bearerToken**, use o operador Equals com o seletor definido como **bearerToken**.
- **Começa com**: Este operador corresponde a todos os campos que começam com o valor de seletor especificado.
- **Termina com**: Este operador corresponde a todos os campos de solicitação que terminam com o valor do seletor especificado.
- **Contains**: esse operador corresponde a todos os campos de solicitação que contêm o valor de seletor especificado.
- **Equals any**: Este operador corresponde a todos os campos de solicitação. * será o valor do seletor.

Em todas as ocorrências correspondentes, não diferencia maiúsculas de minúsculas e a expressão regular não são permitidas como seletores.

> [!NOTE]
> Para obter mais informações e ajuda para solução de problemas, consulte [solução de problemas do WAF](web-application-firewall-troubleshoot.md).

### <a name="examples"></a>Exemplos

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Os exemplos a seguir demonstram o uso de exclusões.

#### <a name="example-1"></a>Exemplo 1

Neste exemplo, você deseja excluir o cabeçalho do agente do usuário. O cabeçalho de solicitação do agente de usuário contém uma cadeia de caracteres característica que permite que os pares de protocolo de rede identifiquem o tipo de aplicativo, sistema operacional, fornecedor de software ou a versão de software do agente de usuário de software solicitante. Para obter mais informações, consulte [User-Agent](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/User-Agent).

Pode haver vários motivos para desabilitar a avaliação desse cabeçalho. Pode haver uma cadeia de caracteres que o WAF vê e supõe que seja mal-intencionado. Por exemplo, o ataque SQL clássico "x = x" em uma cadeia de caracteres. Em alguns casos, isso pode ser um tráfego legítimo. Portanto, talvez seja necessário excluir esse cabeçalho da avaliação do WAF.

O cmdlet a seguir Azure PowerShell exclui o cabeçalho do agente do usuário da avaliação:

```azurepowershell
$exclusion1 = New-AzApplicationGatewayFirewallExclusionConfig `
   -MatchVariable "RequestHeaderNames" `
   -SelectorMatchOperator "Equals" `
   -Selector "User-Agent"
```
#### <a name="example-2"></a>Exemplo 2

Este exemplo exclui o valor no parâmetro *User* que é passado na solicitação por meio da URL. Por exemplo, digamos que seja comum em seu ambiente para o campo de usuário conter uma cadeia de caracteres que o WAF exibe como conteúdo mal-intencionado, para que ele o bloqueie.  Você pode excluir o parâmetro de usuário nesse caso para que o WAF não avalie nada no campo.

O cmdlet a seguir Azure PowerShell exclui o parâmetro do usuário da avaliação:

```azurepowershell
$exclusion2 = New-AzApplicationGatewayFirewallExclusionConfig `
   -MatchVariable "RequestArgNames" `
   -SelectorMatchOperator "StartsWith" `
   -Selector "user"
```
Portanto, se a URL `http://www.contoso.com/?user%281%29=fdafdasfda` for passada para o WAF, ela não avaliará a cadeia de caracteres **fdafdasfda**, mas ainda avaliará o nome do parâmetro **usuário %281 %29**. 

## <a name="waf-request-size-limits"></a>Limites de tamanho de solicitação do WAF



O Firewall do aplicativo Web permite configurar limites de tamanho de solicitação em limites inferiores e superiores. As seguintes configurações de limites de tamanho estão disponíveis:

- O campo tamanho máximo do corpo da solicitação é especificado em quilobytes e controla o limite de tamanho de solicitação geral, excluindo todos os carregamentos de arquivo. Esse campo pode variar de um mínimo de 1 KB para o valor máximo de 128 KB. O valor padrão para o tamanho do corpo da solicitação é 128 KB.
- O campo limite de carregamento de arquivo é especificado em MB e controla o tamanho máximo de upload de arquivo permitido. Esse campo pode ter um valor mínimo de 1 MB e um máximo de 500 MB para instâncias de SKU grande enquanto a SKU média tem um máximo de 100 MB. O valor padrão para o limite de upload de arquivo é 100 MB.

O WAF também oferece um botão configurável para ativar ou desativar a inspeção do corpo da solicitação. Por padrão, a inspeção do corpo da solicitação está habilitada. Se a inspeção do corpo da solicitação for desativada, WAF não avaliará o conteúdo do corpo da mensagem HTTP. Nesses casos, o WAF continua a impor regras de WAF em cabeçalhos, cookies e URI. Se a inspeção do corpo da solicitação estiver desativada, o campo tamanho máximo do corpo da solicitação não será aplicável e não poderá ser definido. A desativação da inspeção do corpo da solicitação permite que as mensagens com mais de 128 KB sejam enviadas para WAF, mas o corpo da mensagem não é inspecionado quanto a vulnerabilidades.

## <a name="next-steps"></a>Passos seguintes

Depois de definir as configurações do WAF, você pode aprender a exibir os logs do WAF. Para obter mais informações, consulte [diagnóstico do gateway de aplicativo](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging).
