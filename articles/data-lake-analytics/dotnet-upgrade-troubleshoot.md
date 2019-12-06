---
title: Como solucionar problemas de falhas no trabalho de U-SQL Azure Data Lake Analytics devido à atualização do .NET 4.7.2
description: Solucionar falhas de trabalho do U-SQL devido à atualização para o .NET 4.7.2.
services: data-lake-analytics
author: guyhay
ms.author: guyhay
ms.reviewer: jasonwhowell
ms.service: data-lake-analytics
ms.topic: troubleshooting
ms.workload: big-data
ms.date: 10/11/2019
ms.openlocfilehash: 2be2f50558fef41659c9a3313871b17961f6ad6d
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873238"
---
# <a name="azure-data-lake-analytics-is-upgrading-to-the-net-framework-v472"></a>Azure Data Lake Analytics está atualizando para o .NET Framework v 4.7.2

O tempo de execução padrão do Azure Data Lake Analytics está atualizando de .NET Framework v 4.5.2 para .NET Framework v 4.7.2. Essa alteração apresenta um pequeno risco de alterações significativas se o código U-SQL usa assemblies personalizados, e esses assemblies personalizados usam bibliotecas .NET.

Essa atualização de .NET Framework 4.5.2 para a versão 4.7.2 significa que o .NET Framework implantado em um tempo de execução do U-SQL (o tempo de execução padrão) agora será sempre 4.7.2. Não há uma opção lado a lado para versões de .NET Framework.

Depois que essa atualização para o .NET 4.7.2 for concluída, o código gerenciado do sistema será executado como a versão 4.7.2, as bibliotecas fornecidas pelo usuário, como os assemblies personalizados do U-SQL, serão executadas no modo compatível com versões anteriores para a versão que o assembly foi gerado fins.

- Se as DLLs do assembly forem geradas para a versão 4.5.2, a estrutura implantada as tratará como bibliotecas 4.5.2, fornecendo (com algumas exceções) a semântica 4.5.2.
- Agora você pode usar assemblies personalizados do U-SQL que usam recursos da versão 4.7.2, se você direcionar para o .NET Framework 4.7.2.

Devido a essa atualização para o .NET 4.7.2, há um potencial para introduzir alterações significativas em seus trabalhos do U-SQL que usam assemblies personalizados do .NET. Sugerimos que você verifique se há problemas de compatibilidade com versões anteriores usando o procedimento abaixo.

## <a name="how-to-check-for-backwards-compatibility-issues"></a>Como verificar se há problemas de compatibilidade com versões anteriores

Verifique o potencial de problemas de interrupção da compatibilidade com versões anteriores executando as verificações de compatibilidade do .NET em seu código .NET em seus assemblies personalizados do U-SQL.

> [!Note]
> A ferramenta não detecta alterações significativas reais. Ele identifica apenas as APIs do .NET que podem (para determinadas entradas) causar problemas. Se você for notificado de um problema, seu código ainda poderá estar correto, mas você deve fazer o check-in de mais detalhes.

1. Execute o verificador de compatibilidade com versões anteriores em suas DLLs do .NET por
   1. Usando a extensão do Visual Studio na [extensão do .net portabilidade Analyzer Visual Studio](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer)
   1. Baixando e usando a ferramenta autônoma do [GitHub dotnetapiport](https://github.com/microsoft/dotnet-apiport). As instruções para executar a ferramenta autônoma estão no [GitHub dotnetapiport alterações significativas](https://github.com/microsoft/dotnet-apiport/blob/dev/docs/HowTo/BreakingChanges.md)
   1. Para 4.7.2. compatibilidade `read isRetargeting == True` identifica possíveis problemas.
2. Se a ferramenta indicar se o código pode ser afetado por qualquer uma das possíveis incompatibilidades com versões anteriores (alguns exemplos comuns de incompatibilidades estão listados abaixo), você pode verificar novamente por
   1. Analisando seu código e identificando se seu código está passando valores para as APIs afetadas
   1. Execute uma verificação de tempo de execução. A implantação de tempo de execução não é feita lado a lado no ADLA. Você pode executar uma verificação de tempo de execução antes da atualização, usando a execução local do VisualStudio com um .NET Framework local 4.7.2 em relação a um conjunto de dados representativo.
3. Se você realmente for afetado por uma incompatibilidade com versões anteriores, execute as etapas necessárias para corrigi-lo (como corrigir sua lógica de dados ou de código).

Na maioria dos casos, você não deve ser afetado pela incompatibilidade com versões anteriores.

## <a name="timeline"></a>Linha cronológica

Você pode verificar a implantação do novo tempo de execução aqui, [solucionar problemas de tempo de execução](runtime-troubleshoot.md)e examinando qualquer trabalho bem-sucedido anterior.

### <a name="what-if-i-cant-get-my-code-reviewed-in-time"></a>E se eu não puder fazer com que meu código seja revisado no tempo

Você pode enviar seu trabalho contra a versão de tempo de execução antiga (que é construída de destino 4.5.2), no entanto, devido à falta de .NET Framework recursos lado a lado, ele ainda será executado apenas no modo de compatibilidade 4.5.2. Você ainda pode encontrar alguns dos problemas de compatibilidade com versões anteriores devido a esse comportamento.

### <a name="what-are-the-most-common-backwards-compatibility-issues-you-may-encounter"></a>Quais são os problemas mais comuns de compatibilidade com versões anteriores que você pode encontrar

As incompatibilidades com versões anteriores mais comuns que o verificador provavelmente identificarão são (geramos essa lista executando o verificador em nossos próprios trabalhos ADLA internos), quais bibliotecas são afetadas (Observação: você pode chamar as bibliotecas somente indiretamente, portanto, é é importante tomar as medidas necessárias #1 para verificar se os trabalhos foram afetados) e as possíveis ações a serem corrigidas. Observação: em quase todos os casos para nossos próprios trabalhos, os avisos desativados como falsos positivos devido à natureza estreita da maioria das alterações mais recentes.

- A propriedade IAsyncResult. CompletedSynchronously deve estar correta para que a tarefa resultante seja concluída
  - Ao chamar TaskFactory. FromAsync, a implementação da propriedade IAsyncResult. CompletedSynchronously deve estar correta para que a tarefa resultante seja concluída. Ou seja, a propriedade deve retornar true se, e somente If, a implementação tiver sido concluída de forma síncrona. Anteriormente, a propriedade não foi verificada.
  - Bibliotecas afetadas: mscorlib, System. Threading. Tasks
  - Ação sugerida: Verifique se TaskFactory. FromAsync retorna true corretamente

- DataObject. GetData agora recupera dados como UTF-8
  - Para aplicativos direcionados para o .NET Framework 4 ou executados no .NET Framework 4.5.1 ou versões anteriores, DataObject. GetData recupera dados formatados em HTML como uma cadeia de caracteres ASCII. Como resultado, caracteres não ASCII (caracteres cujos códigos ASCII são maiores que 0x7F) são representados por dois caracteres aleatórios. #N # #N # para aplicativos direcionados para o .NET Framework 4,5 ou posterior e executados no .NET Framework 4.5.2, `DataObject.GetData` recupera dados formatados em HTML como UTF-8, que representa caracteres maiores que 0x7F corretamente.
  - Bibliotecas afetadas: Glo
  - Ação sugerida: Verifique se os dados recuperados são do formato que você deseja

- O XmlWriter é acionado em pares substitutos inválidos
  - Para aplicativos direcionados para o .NET Framework 4.5.2 ou versões anteriores, escrever um par alternativo inválido usando a manipulação de fallback de exceção nem sempre gera uma exceção. Para aplicativos direcionados para o .NET Framework 4,6, a tentativa de gravar um par substituto inválido gera um `ArgumentException`.
  - Bibliotecas afetadas: System. xml, System. xml. ReaderWriter
  - Ação sugerida: Verifique se você não está gravando um par substituto inválido que causará exceção de argumento

- HtmlTextWriter não processa `<br/>` elemento corretamente
  - A partir do .NET Framework 4,6, chamar `HtmlTextWriter.RenderBeginTag()` e `HtmlTextWriter.RenderEndTag()` com um elemento `<BR />` inserirá corretamente apenas um `<BR />` (em vez de dois)
  - Bibliotecas afetadas: System. Web
  - Ação sugerida: Verifique se você está inserindo a quantidade de `<BR />` esperada para ver que nenhum comportamento aleatório é visto no trabalho de produção

- A chamada a CreateDefaultAuthorizationContext com um argumento nulo foi alterada
  - A implementação do AuthorizationContext retornado por uma chamada para o `CreateDefaultAuthorizationContext(IList<IAuthorizationPolicy>)` com um argumento authorizationPolicies nulo alterou sua implementação no .NET Framework 4,6.
  - Bibliotecas afetadas: System. IdentityModel
  - Ação sugerida: Verifique se você está lidando com o novo comportamento esperado quando há uma política de autorização nula
  
- RSACng agora carrega corretamente chaves RSA de tamanho de chave não padrão
  - Em versões .NET Framework anteriores ao 4.6.2, os clientes com tamanhos de chave não padrão para certificados RSA não podem acessar essas chaves por meio dos métodos de extensão `GetRSAPublicKey()` e `GetRSAPrivateKey()`. Uma `CryptographicException` com a mensagem "o tamanho da chave solicitada não é suportada" é lançada. Com o .NET Framework 4.6.2, esse problema foi corrigido. Da mesma forma, `RSA.ImportParameters()` e `RSACng.ImportParameters()` agora funcionam com tamanhos de chave não padrão sem lançar `CryptographicException`.
  - Bibliotecas afetadas: mscorlib, System. Core
  - Ação sugerida: Verifique se as chaves RSA estão funcionando conforme o esperado

- As verificações de dois pontos de caminho são mais rígidas
  - No .NET Framework 4.6.2, várias alterações foram feitas para dar suporte a caminhos anteriormente não suportados (tanto no comprimento quanto no formato). Verifica se a sintaxe apropriada do separador de unidade (dois-pontos) foi feita mais corretamente, o que teve o efeito colateral de bloquear alguns caminhos de URI em algumas APIs de caminho Select, nas quais costumava ser tolerado.
  - Bibliotecas afetadas: mscorlib, System. Runtime. Extensions
  - Ação sugerida:

- Chamadas para construtores ClaimsIdentity
  - Começando com o .NET Framework 4.6.2, há uma alteração em como `T:System.Security.Claims.ClaimsIdentity` construtores com um parâmetro `T:System.Security.Principal.IIdentity` definem a propriedade `P:System.Security.Claims.ClaimsIdentify.Actor`. Se o argumento `T:System.Security.Principal.IIdentity` for um objeto `T:System.Security.Claims.ClaimsIdentity` e a propriedade `P:System.Security.Claims.ClaimsIdentify.Actor` desse objeto `T:System.Security.Claims.ClaimsIdentity` não for `null`, a propriedade `P:System.Security.Claims.ClaimsIdentify.Actor` será anexada usando o método `M:System.Security.Claims.ClaimsIdentity.Clone`. No Framework 4.6.1 e versões anteriores, a propriedade `P:System.Security.Claims.ClaimsIdentify.Actor` é anexada como uma referência existente. Por causa dessa alteração, começando com o .NET Framework 4.6.2, a propriedade `P:System.Security.Claims.ClaimsIdentify.Actor` do novo objeto `T:System.Security.Claims.ClaimsIdentity` não é igual à propriedade `P:System.Security.Claims.ClaimsIdentify.Actor` do argumento de `T:System.Security.Principal.IIdentity` do construtor. No .NET Framework 4.6.1 e versões anteriores, é igual.
  - Bibliotecas afetadas: mscorlib
  - Ação sugerida: Verifique se ClaimsIdentity está funcionando conforme o esperado no novo tempo de execução

- A serialização de caracteres de controle com DataContractJsonSerializer agora é compatível com ECMAScript V6 e V8
  - No .NET Framework 4.6.2 e versões anteriores, o DataContractJsonSerializer não serializava alguns caracteres de controle especiais, como \b, \f e \t, de forma que fosse compatível com os padrões ECMAScript V6 e V8. A partir do .NET Framework 4,7, a serialização desses caracteres de controle é compatível com ECMAScript V6 e V8.
  - Bibliotecas afetadas: System. Runtime. Serialization. JSON
  - Ação sugerida: Verifique o mesmo comportamento com DataContractJsonSerializer
