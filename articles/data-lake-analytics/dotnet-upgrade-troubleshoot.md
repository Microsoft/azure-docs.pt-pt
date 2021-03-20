---
title: Como resolver problemas com as falhas de emprego no Azure Data Lake Analytics U-SQL devido à atualização do .NET Framework 4.7.2
description: Resolução de problemas Falhas de trabalho U-SQL devido à atualização para .NET Framework 4.7.2.
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: troubleshooting
ms.date: 10/11/2019
ms.openlocfilehash: ab03ea8a88187289f5dce55f8a396a9d51346a3f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92217682"
---
# <a name="azure-data-lake-analytics-is-upgrading-to-the-net-framework-v472"></a>A Azure Data Lake Analytics está a atualizar para o .NET Framework v4.7.2

O tempo de funcionamento padrão do Azure Data Lake Analytics está a ser melhorado de .NET Framework v4.5.2 para .NET Framework v4.7.2. Esta alteração introduz um pequeno risco de rutura se o seu código U-SQL utilizar conjuntos personalizados e esses conjuntos personalizados utilizarem bibliotecas .NET.

Esta atualização do Quadro 4.5.2 da NET para a versão 4.7.2 significa que o Quadro .NET implantado num tempo de funcionamento U-SQL (o tempo de funcionamento padrão) será agora sempre 4.7.2. Não existe uma opção lado a lado para as versões .NET Framework.

Após esta atualização para .NET Framework 4.7.2 estar concluída, o código gerido do sistema será executado como versão 4.7.2, desde que as bibliotecas fornecidas pelo utilizador, como os conjuntos personalizados U-SQL, sejam executadas no modo retrocompatível adequado à versão para a qual o conjunto foi gerado.

- Se os DLLs de montagem forem gerados para a versão 4.5.2, o quadro implantado tratará-os como 4.5.2 bibliotecas, proporcionando (com algumas exceções) 4.5.2 semântica.
- Agora pode utilizar conjuntos personalizados U-SQL que utilizam as funcionalidades da versão 4.7.2, se tiver como alvo o Quadro .NET 4.7.2.

Devido a esta atualização para .NET Framework 4.7.2, existe o potencial de introduzir alterações de rutura nos seus trabalhos U-SQL que utilizam conjuntos personalizados .NET. Sugerimos que verifique se há problemas de retrocompatibilidade utilizando o procedimento abaixo.

## <a name="how-to-check-for-backwards-compatibility-issues"></a>Como verificar se há problemas de retrocompatibilidade

Verifique se existe o potencial de quebra de retrocompatibilidade, executando as verificações de compatibilidade .NET no seu código .NET nos seus conjuntos personalizados U-SQL.

> [!Note]
> A ferramenta não deteta alterações reais de rutura. identifica apenas as chamadas APIs .NET que podem (para determinadas entradas) causar problemas. Se for notificado de um problema, o seu código pode ainda estar bem, no entanto deverá verificar mais detalhes.

1. Passe o verificador de retrocompatibilidade nos seus DLLs .NET quer por
   1. Utilização da extensão do estúdio visual em [.NET Portaability Analyzer Visual Studio Extension](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer)
   1. Descarregar e utilizar a ferramenta autónoma do [GitHub dotnetapiport](https://github.com/microsoft/dotnet-apiport). Instruções para executar ferramenta autónoma estão no [GitHub dotnetapiport quebrando alterações](https://github.com/microsoft/dotnet-apiport/blob/dev/docs/HowTo/BreakingChanges.md)
   1. Por 4.7.2. compatibilidade, `read isRetargeting == True` identifica possíveis problemas.
2. Se a ferramenta indicar se o seu código pode ser impactado por qualquer uma das possíveis incompatibilidades para trás (alguns exemplos comuns de incompatibilidades estão listados abaixo), pode verificar por
   1. Analisar o seu código e identificar se o seu código está a passar valores para as APIs impactadas
   1. Faça uma verificação de tempo de execução. A colocação do tempo de execução não é feita lado a lado na ADLA. Pode efetuar uma verificação de tempo de execução antes da atualização, utilizando a execução local do VisualStudio com um Quadro 4.NET local 4.7.2 contra um conjunto de dados representativo.
3. Se for realmente afetado por uma incompatibilidade inversa, tome as medidas necessárias para a corrigir (como a fixação dos seus dados ou lógica de código).

Na maioria dos casos, não deve ser afetado por incompatibilidades.

## <a name="timeline"></a>Linha cronológica

Você pode verificar a implementação do novo tempo de execução aqui [Runtime troubleshoot](runtime-troubleshoot.md), e olhando para qualquer trabalho de sucesso anterior.

### <a name="what-if-i-cant-get-my-code-reviewed-in-time"></a>E se eu não conseguir rever o meu código a tempo?

Pode submeter o seu trabalho contra a versão antiga do tempo de execução (que é construída como alvo 4.5.2), no entanto, devido à falta de capacidades de .NET Framework lado a lado, ele ainda só funcionará em modo de compatibilidade 4.5.2. Ainda pode encontrar alguns dos problemas de retrocompatibilidade por causa deste comportamento.

### <a name="what-are-the-most-common-backwards-compatibility-issues-you-may-encounter"></a>Quais são as questões de retrocompatibilidade mais comuns que pode encontrar

As incompatibilidades mais comuns que o verificador é suscetível de identificar são (gerámos esta lista executando o verificador nos nossos próprios trabalhos internos de ADLA), que as bibliotecas são impactadas (nota: que pode ligar para as bibliotecas apenas indiretamente, pelo que é importante tomar as medidas necessárias #1 para verificar se os seus empregos são impactados) e possíveis ações para remediar. Nota: Em quase todos os casos para os nossos próprios empregos, os avisos revelaram-se falsos positivos devido à natureza estreita da maioria das mudanças.

- IAsyncResult.CompletedSynchronously propriedade deve estar correta para que a tarefa resultante seja concluída
  - Ao chamar TaskFactory.FromAsync, a implementação da propriedade IAsyncResult.CompletedSynchronously deve estar correta para que a tarefa resultante esteja concluída. Ou seja, a propriedade deve devolver a verdade se, e somente se, a implementação concluída de forma sincronizada. Anteriormente, a propriedade não foi verificada.
  - Bibliotecas impactadas: mscorlib, System.Threading.Tasks
  - Ação sugerida: Garantir a TaskFactory.FromAsync retorna corretamente

- DataObject.GetData agora recupera dados como UTF-8
  - Para aplicações que direcionem o Quadro .NET 4 ou que funcionam nas versões .NET Framework 4.5.1 ou anteriores, o DataObject.GetData recupera dados formatados com HTML como uma cadeia ASCII. Como resultado, os caracteres não ASCII (caracteres cujos códigos ASCII são superiores a 0x7F) são representados por duas aplicações aleatórias caracteres.#N##N#For que visam o Quadro .NET 4.5 ou posterior e funcionam no Quadro .NET 4.5.2, `DataObject.GetData` recupera dados com formato HTML como UTF-8, que representa caracteres superiores a 0x7F corretamente.
  - Bibliotecas impactadas: Glo
  - Ação Sugerida: Garantir que os dados recuperados é o formato que pretende

- XmlWriter lança pares de substitutos inválidos
  - Para aplicações que visam as versões .NET Framework 4.5.2 ou anteriores, escrever um par de substitutos inválido utilizando o manuseamento de recuos de exceção nem sempre abre uma exceção. Para aplicações que visam o Quadro .NET 4.6, tentar escrever um par de substitutos inválido lança um `ArgumentException` .
  - Bibliotecas impactadas: System.Xml, System.Xml. LeitorDista
  - Ação Sugerida: Certifique-se de que não está a escrever um par de substitutos inválido que irá causar exceção ao argumento

- HtmlTextWriter não torna `<br/>` o elemento corretamente
  - A partir do Quadro .NET 4.6, a chamada `HtmlTextWriter.RenderBeginTag()` e com um elemento `HtmlTextWriter.RenderEndTag()` `<BR />` inserirão corretamente apenas um `<BR />` (em vez de dois)
  - Bibliotecas impactadas: System.Web
  - Ação Sugerida: Certifique-se de que está a inserir a quantidade que espera ver para que nenhum comportamento aleatório seja visto no trabalho de `<BR />` produção

- Chamando CreateDefaultAuthorizationContext com um argumento nulo mudou
  - A implementação do Acordo de Autorização devolvido por um apelo ao `CreateDefaultAuthorizationContext(IList<IAuthorizationPolicy>)` com autorização nulo O argumento da Política alterou a sua implementação no Quadro .NET 4.6.
  - Bibliotecas Impactadas: System.IdentityModel
  - Ação Sugerida: Certifique-se de que está a lidar com o novo comportamento esperado quando há uma política de autorização nula
  
- RSACng agora carrega corretamente chaves RSA de tamanho de chave não padrão
  - Nas versões .NET Framework antes do ponto 4.6.2, os clientes com tamanhos-chave não padrão para certificados RSA não conseguem aceder a essas chaves através dos `GetRSAPublicKey()` métodos de extensão e `GetRSAPrivateKey()` extensão. A `CryptographicException` com a mensagem "O tamanho da chave solicitado não é suportado" é lançado. Com o Quadro .NET 4.6.2 esta emissão foi corrigida. Da mesma forma, `RSA.ImportParameters()` e agora trabalhar com `RSACng.ImportParameters()` tamanhos de chave não padrão sem `CryptographicException` atirar's.
  - Bibliotecas impactadas: mscorlib, System.Core
  - Ação sugerida: Garanta que as chaves RSA estão a funcionar como esperado

- As verificações do cólon do caminho são mais rigorosas
  - No quadro .NET 4.6.2, foram efetuadas algumas alterações para suportar caminhos anteriormente não apoiados (tanto em comprimento como em formato). Foram feitas verificações de sintaxe de separador de unidade adequada (cólon), o que teve o efeito colateral de bloquear alguns caminhos URI em algumas APIs de caminho selecionados onde costumavam ser tolerados.
  - Bibliotecas impactadas: mscorlib, System.Runtime.Extensions
  - Ação sugerida:

- Chamadas para construtores de entidades reivindicatórias
  - A partir do Quadro .NET 4.6.2, há uma alteração na forma como `T:System.Security.Claims.ClaimsIdentity` os construtores com um `T:System.Security.Principal.IIdentity` parâmetro definem o `P:System.Security.Claims.ClaimsIdentify.Actor` imóvel. Se o `T:System.Security.Principal.IIdentity` argumento for um `T:System.Security.Claims.ClaimsIdentity` objeto, e a propriedade desse `P:System.Security.Claims.ClaimsIdentify.Actor` objeto não `T:System.Security.Claims.ClaimsIdentity` `null` for, a propriedade é `P:System.Security.Claims.ClaimsIdentify.Actor` anexada usando o `M:System.Security.Claims.ClaimsIdentity.Clone` método. Nas versões 4.6.1 e anteriores, a `P:System.Security.Claims.ClaimsIdentify.Actor` propriedade é anexada como referência existente. Devido a esta alteração, a começar pelo Quadro .NET 4.6.2, a `P:System.Security.Claims.ClaimsIdentify.Actor` propriedade do novo objeto não é igual à propriedade do argumento do `T:System.Security.Claims.ClaimsIdentity` `P:System.Security.Claims.ClaimsIdentify.Actor` `T:System.Security.Principal.IIdentity` construtor. Nas versões .NET Framework 4.6.1 e anteriores, é igual.
  - Bibliotecas Impactadas: mscorlib
  - Ação Sugerida: Garantir que a ClaimsIdentity está a funcionar como esperado em novos tempos de funcionamento

- A serialização de caracteres de controlo com DataContractJsonSerializer é agora compatível com ECMAScript V6 e V8
  - No quadro .NET 4.6.2 e nas versões anteriores, o DataContractJsonSerializer não serializou alguns caracteres de controlo especiais, tais como \b, \f e \t, de uma forma compatível com as normas ECMAScript V6 e V8. A partir do Quadro .NET 4.7, a serialização destes caracteres de controlo é compatível com ECMAScript V6 e V8.
  - Bibliotecas impactadas: System.Runtime.Serialization.Jsem
  - Ação Sugerida: Garantir o mesmo comportamento com DataContractJsonSerializer
