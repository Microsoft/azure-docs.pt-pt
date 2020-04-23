---
title: Como resolver os problemas com as falhas de emprego do Azure Data Lake Analytics U-SQL por causa da atualização .NET Framework 4.7.2
description: Problemas de resolução de emprego suql por causa da atualização para .NET Framework 4.7.2.
services: data-lake-analytics
author: guyhay
ms.author: guyhay
ms.reviewer: jasonwhowell
ms.service: data-lake-analytics
ms.topic: troubleshooting
ms.workload: big-data
ms.date: 10/11/2019
ms.openlocfilehash: f909419810cbd837e57b19a13b2df6ae9ad2ee97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79213589"
---
# <a name="azure-data-lake-analytics-is-upgrading-to-the-net-framework-v472"></a>O Azure Data Lake Analytics está a atualizar para o .NET Framework v4.7.2

O tempo de execução padrão do Azure Data Lake Analytics está a atualizar de .NET Framework v4.5.2 para .NET Framework v4.7.2. Esta alteração introduz um pequeno risco de quebrar alterações se o seu código U-SQL utilizar conjuntos personalizados, e esses conjuntos personalizados usam bibliotecas .NET.

Esta atualização do .NET Framework 4.5.2 para a versão 4.7.2 significa que o Quadro .NET implantado num tempo de funcionação U-SQL (o tempo de execução padrão) será agora sempre de 4.7.2. Não existe uma opção lado a lado para as versões .NET Framework.

Após esta atualização para .NET Framework 4.7.2 estar concluída, o código gerido do sistema será executado como versão 4.7.2, o utilizador fornecido bibliotecas como os conjuntos personalizados U-SQL funcionará no modo compatível para trás adequado para a versão para a qual o conjunto foi gerado.

- Se os DLLs de montagem forem gerados para a versão 4.5.2, o quadro implantado irá tratá-los como 4.5.2 bibliotecas, proporcionando (com algumas exceções) 4.5.2 semântica.
- Agora pode utilizar conjuntos personalizados U-SQL que utilizam as funcionalidades da versão 4.7.2, se tiver como alvo o .NET Framework 4.7.2.

Devido a esta atualização para .NET Framework 4.7.2, existe o potencial de introduzir alterações de rutura nos seus trabalhos U-SQL que usam conjuntos personalizados .NET. Sugerimos que verifique se há problemas de retrocompatibilidade utilizando o procedimento abaixo.

## <a name="how-to-check-for-backwards-compatibility-issues"></a>Como verificar se há problemas de retrocompatibilidade

Verifique o potencial de problemas de quebra de compatibilidade ao executar os controlos de compatibilidade .NET no seu código .NET nos seus conjuntos personalizados U-SQL.

> [!Note]
> A ferramenta não deteta alterações reais de rutura. identifica apenas as APIs chamadas .NET que podem (para determinadas inputs) causar problemas. Se for notificado de um problema, o seu código ainda pode estar bem, no entanto deverá verificar mais detalhes.

1. Executar o verificador de retrocompatibilidade nos seus DLLs .NET, quer por
   1. Utilizando a extensão do estúdio visual na extensão do estúdio visual de Analisador de [Portabilidade .NET](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer)
   1. Descarregar e utilizar a ferramenta autónoma a partir do [GitHub dotnetapiport](https://github.com/microsoft/dotnet-apiport). Instruções para executar ferramenta autónoma estão em alterações de quebra do [dotnetapiport GitHub](https://github.com/microsoft/dotnet-apiport/blob/dev/docs/HowTo/BreakingChanges.md)
   1. Para 4.7.2. compatibilidade, `read isRetargeting == True` identifica possíveis problemas.
2. Se a ferramenta indicar se o seu código pode ser impactado por qualquer uma das possíveis incompatibilidades para trás (alguns exemplos comuns de incompatibilidades estão listados abaixo), pode verificar por
   1. Analisar o seu código e identificar se o seu código está a passar valores para as APIs impactadas
   1. Faça uma verificação de tempo de execução. O tempo de execução não é feito lado a lado na ADLA. Pode efetuar uma verificação de tempo de execução antes da atualização, utilizando a execução local do VisualStudio com uma .NET Framework 4.7.2 local contra um conjunto de dados representativo.
3. Se for mesmo afetado por uma incompatibilidade ao contrário, tome as medidas necessárias para corrigi-lo (como a fixação dos seus dados ou lógica de código).

Na maioria dos casos, não deve ser afetado pela incompatibilidade.

## <a name="timeline"></a>Linha cronológica

Você pode verificar a implementação do novo tempo de execução aqui [Runtime troubleshoot](runtime-troubleshoot.md), e olhando para qualquer trabalho de sucesso anterior.

### <a name="what-if-i-cant-get-my-code-reviewed-in-time"></a>E se eu não conseguir que o meu código seja revisto a tempo?

Pode submeter o seu trabalho contra a versão antiga do tempo de funcionamento (que é construída com o objetivo 4.5.2), no entanto, devido à falta de capacidades de .NET Framework lado a lado, ainda funcionará apenas em modo de compatibilidade 4.5.2. Ainda pode encontrar alguns dos problemas de retrocompatibilidade por causa deste comportamento.

### <a name="what-are-the-most-common-backwards-compatibility-issues-you-may-encounter"></a>Quais são as questões de retrocompatibilidade mais comuns que pode encontrar

As incompatibilidades mais comuns que o verificador é suscetível de identificar são (gerámos esta lista executando o verificador nos nossos próprios empregos internos da ADLA), quais as bibliotecas que são impactadas (nota: que pode chamar as bibliotecas apenas indiretamente, pelo que é importante tomar medidas necessárias #1 verificar se os seus empregos são impactados) e possíveis ações para remediar. Nota: Em quase todos os casos para o nosso próprio trabalho, os avisos revelaram-se falsos positivos devido às naturezas estreitas da maioria das mudanças de rutura.

- IAsyncResult.CompletedSynchronously property must be correct for the resulta task to complete
  - Ao chamar TaskFactory.FromAsync, a implementação da iAsyncResult.CompletedSynchronously property deve estar correta para que a tarefa resultante seja concluída. Ou seja, a propriedade deve voltar a ser verdadeira se, e apenas se, a implementação concluída sincronizadamente. Anteriormente, a propriedade não foi verificada.
  - Bibliotecas impactadas: mscorlib, System.Threading.Tasks
  - Ação sugerida: Garantir taskFactory.FromAsync devolve corretamente

- DataObject.GetData agora recupera dados como UTF-8
  - Para aplicações que visam a .NET Framework 4 ou que funcionam nas versões .NET Framework 4.5.1 ou anteriores, DataObject.GetData recupera dados formados em HTML como uma cadeia ASCII. Como resultado, os caracteres não-ASCII (caracteres cujos códigos ASCII são superiores a 0x7F) são representados por dois caracteres aleatórios.#N##N#Para aplicações que `DataObject.GetData` visam a .NET Framework 4.5 ou posteriormente e funcionam no .NET Framework 4.5.2, recupera dados formados em HTML como UTF-8, que representa caracteres superiores a 0x7F corretamente.
  - Bibliotecas impactadas: Glo
  - Ação Sugerida: Certifique-se de que os dados recuperados são o formato que deseja

- XmlWriter lança pares de substitutos inválidos
  - Para aplicações que visam o .NET Framework 4.5.2 ou versões anteriores, escrever um par de substitutos inválido usando o manuseamento de recuo sem exceção nem sempre abre uma exceção. Para aplicações que visam o .NET Framework 4.6, tentar escrever `ArgumentException`um par de substitutos inválido lança um .
  - Bibliotecas impactadas: System.Xml, System.Xml.ReaderWriter
  - Ação Sugerida: Certifique-se de que não está a escrever um par de substitutos inválido que irá causar exceção ao argumento

- HtmlTextWriter não `<br/>` presta corretamente o elemento
  - A partir do .NET Quadro `HtmlTextWriter.RenderBeginTag()` 4.6, a chamada e `HtmlTextWriter.RenderEndTag()` com um `<BR />` elemento inserirão corretamente apenas um `<BR />` (em vez de dois)
  - Bibliotecas impactadas: System.Web
  - Ação Sugerida: Certifique-se de `<BR />` que está a inserir a quantidade de que espera ver para que nenhum comportamento aleatório seja visto no trabalho de produção

- Chamar createDefaultAuthorizationContext com um argumento nulo mudou
  - A implementação do Contexto de Autorizações devolvida por um apelo ao `CreateDefaultAuthorizationContext(IList<IAuthorizationPolicy>)` com uma autorização nula O argumento das políticas alterou a sua implementação no .NET Framework 4.6.
  - Bibliotecas impactadas: System.IdentityModel
  - Ação Sugerida: Certifique-se de que está a lidar com o novo comportamento esperado quando houver uma política de autorização nula
  
- RSACng agora carrega corretamente chaves RSA de tamanho de chave não padrão
  - Nas versões .NET Framework antes de 4.6.2, os clientes com tamanhos-chave não standard `GetRSAPublicKey()` para `GetRSAPrivateKey()` certificados RSA não conseguem aceder a essas teclas através dos métodos de extensão e extensão. A `CryptographicException` com a mensagem "O tamanho da chave solicitado não é suportado" é lançado. Com o Quadro .NET 4.6.2 esta questão foi corrigida. Da mesma `RSA.ImportParameters()` `RSACng.ImportParameters()` forma, e agora trabalhar com `CryptographicException`tamanhos-chave não-padrão sem atirar's.
  - Bibliotecas impactadas: mscorlib, System.Core
  - Ação Sugerida: Certifique-se de que as chaves RSA estão a funcionar como esperado

- Os controlos do cólon do caminho são mais rigorosos
  - No quadro .NET 4.6.2, foram efetuadas várias alterações para apoiar caminhos anteriormente não apoiados (tanto em comprimento como em formato). Os controlos para a sintaxe adequada do separador de unidade (cólon) foram feitos mais corretos, o que teve o efeito colateral de bloquear alguns caminhos URI em algumas APIs de caminho selecionado onde costumavam ser tolerados.
  - Bibliotecas impactadas: mscorlib, System.Runtime.Extensões
  - Ação Sugerida:

- Chamadas para construtores de identidade de reclamações
  - Começando com o .NET Framework 4.6.2, `T:System.Security.Claims.ClaimsIdentity` há uma mudança `T:System.Security.Principal.IIdentity` na forma `P:System.Security.Claims.ClaimsIdentify.Actor` como os construtores com um parâmetro definiram a propriedade. Se `T:System.Security.Principal.IIdentity` o argumento `T:System.Security.Claims.ClaimsIdentity` for um `P:System.Security.Claims.ClaimsIdentify.Actor` objeto, `T:System.Security.Claims.ClaimsIdentity` e `null`a `P:System.Security.Claims.ClaimsIdentify.Actor` propriedade desse objeto `M:System.Security.Claims.ClaimsIdentity.Clone` não for, a propriedade é anexada utilizando o método. No quadro 4.6.1 e versões `P:System.Security.Claims.ClaimsIdentify.Actor` anteriores, o imóvel é anexado como referência existente. Devido a esta alteração, a começar pelo .NET Framework `P:System.Security.Claims.ClaimsIdentify.Actor` 4.6.2, a propriedade do novo `T:System.Security.Claims.ClaimsIdentity` objeto não é igual à `P:System.Security.Claims.ClaimsIdentify.Actor` propriedade do argumento do `T:System.Security.Principal.IIdentity` construtor. No Quadro .NET 4.6.1 e versões anteriores, é igual.
  - Bibliotecas impactadas: mscorlib
  - Ação Sugerida: Garantir que a ClaimsIdentity está a funcionar como esperado em novo prazo

- A serialização dos caracteres de controlo com DataContractJsonSerializer é agora compatível com ECMAScript V6 e V8
  - Na estrutura .NET 4.6.2 e versões anteriores, o DataContractJsonSerializer não serializou alguns caracteres de controlo especiais, tais como \b, \f e \t, de uma forma compatível com as normas ECMAScript V6 e V8. A partir da .NET Framework 4.7, a serialização destes caracteres de controlo é compatível com o ECMAScript V6 e v8.
  - Bibliotecas impactadas: System.Runtime.Serialization.Json
  - Ação Sugerida: Garantir o mesmo comportamento com DataContractJsonSerializer
