---
title: Azure App Configuration FAQ
description: Leia respostas a perguntas frequentes (FAQ) sobre a Configuração da Aplicação Azure, como a sua diferença do Cofre de Chaves Azure.
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: lcozzens
ms.openlocfilehash: ef4633953f7ac03737608124309d94e436913794
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91715448"
---
# <a name="azure-app-configuration-faq"></a>Azure App Configuration FAQ

Este artigo responde frequentemente a perguntas sobre a Configuração da Aplicação Azure.

## <a name="how-is-app-configuration-different-from-azure-key-vault"></a>Como é que a Configuração de Aplicativo é diferente do Cofre da Chave Azure?

A Configuração de Aplicativos ajuda os desenvolvedores a gerir as definições de aplicações e a controlar a disponibilidade de funcionalidades. Visa simplificar muitas das tarefas de trabalhar com dados de configuração complexos.

Suportes de configuração de aplicativos:

- Espaços de nomes hierárquicos
- Rotulagem
- Consultas extensas
- Recuperação de lotes
- Operações de gestão especializadas
- Uma interface de utilizador de gestão de funcionalidades

A Configuração da Aplicação complementa o Key Vault e os dois devem ser usados lado a lado na maioria das implementações da aplicação.

## <a name="should-i-store-secrets-in-app-configuration"></a>Devo armazenar segredos na Configuração de Aplicações?

Embora a Configuração de Aplicações forneça segurança reforçada, o Key Vault ainda é o melhor local para armazenar segredos de aplicações. O Key Vault fornece encriptação de nível de hardware, políticas de acesso granular e operações de gestão, tais como rotação de certificados.

Pode criar valores de Configuração de Aplicações que mencionem segredos armazenados no Cofre de Chaves. Para obter mais informações, consulte [as referências do Key Vault numa aplicação Core ASP.NET](./use-key-vault-references-dotnet-core.md).

## <a name="does-app-configuration-encrypt-my-data"></a>A Configuração da Aplicação encripta os meus dados?

Sim. A Configuração de Aplicativos encripta todos os valores-chave que detém e encripta a comunicação da rede. Os nomes-chave e as etiquetas são usados como índices para recuperar dados de configuração e não são encriptados.

## <a name="where-does-data-stored-in-app-configuration-reside"></a>Onde residem os dados armazenados na Configuração de Aplicações? 

Os dados do cliente armazenados na Configuração de Aplicações residem na região onde foi criada a loja de Configuração de Aplicações do cliente. A Configuração de Aplicativos pode replicar dados para [regiões emparelhadas](https://docs.microsoft.com/azure/best-practices-availability-paired-regions) para resiliência de dados, mas não vai replicar ou mover os dados dos clientes para fora do seu Geo, conforme definido pela [residência de dados em Azure](https://azure.microsoft.com/global-infrastructure/data-residency/). Os clientes e os utilizadores finais podem mover-se, copiar ou aceder aos dados dos seus clientes a partir de qualquer local a nível global.

## <a name="how-is-app-configuration-different-from-azure-app-service-settings"></a>Como é que a Configuração da Aplicação é diferente das definições do Serviço de Aplicações Azure?

O Azure App Service permite-lhe definir as definições de aplicações para cada instância do Serviço de Aplicações. Estas definições são passadas como variáveis ambientais para o código de aplicação. Pode associar uma definição a uma ranhura de implantação específica, se quiser. Para obter mais informações, consulte [as definições da aplicação Configure](/azure/app-service/configure-common#configure-app-settings).

Em contraste, a Configuração de Aplicações Azure permite definir definições que podem ser partilhadas entre várias aplicações. Isto inclui aplicações em execução no Serviço de Aplicações, bem como outras plataformas. O seu código de aplicação acede a estas definições através dos fornecedores de configuração de .NET e Java, através do Azure SDK, ou diretamente através de REST APIs.

Também pode importar e exportar configurações entre o Serviço de Aplicações e a Configuração de Aplicações. Esta capacidade permite-lhe configurar rapidamente uma nova loja de Configuração de Aplicações com base nas definições do Serviço de Aplicações existentes. Também pode partilhar a configuração com uma aplicação existente que se baseia nas definições do Serviço de Aplicações.

## <a name="are-there-any-size-limitations-on-keys-and-values-stored-in-app-configuration"></a>Existem limitações de tamanho nas teclas e valores armazenados na Configuração da Aplicação?

Há um limite de 10 KB para um único item de valor-chave.

## <a name="how-should-i-store-configurations-for-multiple-environments-test-staging-production-and-so-on"></a>Como devo armazenar configurações para vários ambientes (teste, encenação, produção, e assim por diante)?

Você controla quem pode aceder à Configuração de Aplicações a um nível por loja. Utilize uma loja separada para cada ambiente que exija permissões diferentes. Esta abordagem proporciona o melhor isolamento de segurança.

Se não necessitar de isolamento de segurança entre ambientes, pode utilizar etiquetas para diferenciar os valores de configuração. [A utilização de rótulos para permitir configurações diferentes para diferentes ambientes](./howto-labels-aspnet-core.md) fornece um exemplo completo.

## <a name="what-are-the-recommended-ways-to-use-app-configuration"></a>Quais são as formas recomendadas de utilizar a Configuração da Aplicação?

Consulte as [melhores práticas.](./howto-best-practices.md)

## <a name="how-much-does-app-configuration-cost"></a>Quanto custa a Configuração de Aplicações?

Existem dois níveis de preços:

- Escalão gratuito
- Nível padrão.

Se criou uma loja antes da introdução do nível Standard, mudou-se automaticamente para o nível Free mediante disponibilidade geral. Pode optar por fazer upgrade para o nível Standard ou permanecer no nível Livre.

Não se pode desvalorizar uma loja do nível Standard para o nível Free. Pode criar uma nova loja no nível Livre e, em seguida, importar dados de configuração para essa loja.

## <a name="which-app-configuration-tier-should-i-use"></a>Qual o nível de configuração da aplicação que devo utilizar?

Ambos os níveis de configuração de aplicativos oferecem funcionalidades fundamentais, incluindo configurações config, bandeiras de recursos, referências do Cofre-Chave, operações básicas de gestão, métricas e registos.

Seguem-se considerações para a escolha de um nível.

- **Recursos por subscrição**: Um recurso consiste numa única loja de configuração. Cada subscrição é limitada a uma loja de configuração no nível livre. As subscrições podem ter um número ilimitado de lojas de configuração no nível padrão.
- **Armazenamento por recurso**: No nível livre, cada loja de configuração está limitada a 10 MB de armazenamento. No nível padrão, cada loja de configuração pode usar até 1 GB de armazenamento.
- **Histórico chave**: A Configuração de Aplicações armazena um histórico de todas as alterações feitas nas teclas. No nível livre, esta história está armazenada por sete dias. No nível padrão, esta história é armazenada por 30 dias.
- **Pedidos por dia**: As lojas de nível gratuito estão limitadas a 1.000 pedidos por dia. Uma vez que uma loja atinge 1.000 pedidos, devolverá o código de estado HTTP 429 para todos os pedidos até à meia-noite utc.

    Para lojas standard tier, os primeiros 200.000 pedidos por dia estão incluídos na taxa diária. Os pedidos adicionais são contabilizados como excesso de idade.

- **Contrato de nível**de serviço : O nível padrão tem uma disponibilidade de SLA de 99,9%. O nível livre não tem SLA.
- **Funcionalidades de segurança**: Ambos os níveis incluem funcionalidade de segurança básica, incluindo encriptação com teclas geridas pela Microsoft, autenticação via HMAC ou Azure Ative Directory, suporte Azure RBAC e identidade gerida. O standard tier oferece uma funcionalidade de segurança mais avançada, incluindo suporte private link e encriptação com chaves geridas pelo cliente.
- **Custo**: As lojas standard têm uma taxa de utilização diária. Há também uma taxa de excesso de pedidos para além da atribuição diária. Não há custo para usar uma loja de nível livre.

## <a name="can-i-upgrade-a-store-from-the-free-tier-to-the-standard-tier-can-i-downgrade-a-store-from-the-standard-tier-to-the-free-tier"></a>Posso atualizar uma loja do nível Livre para o nível Standard? Posso desvalorizar uma loja do nível Standard para o nível Livre?

Pode atualizar do nível Free para o nível Standard a qualquer momento.

Não se pode desvalorizar uma loja do nível Standard para o nível Free. Pode criar uma nova loja no nível Livre e, em seguida, [importar dados de configuração para essa loja.](howto-import-export-data.md)

## <a name="are-there-any-limits-on-the-number-of-requests-made-to-app-configuration"></a>Existem limites ao número de pedidos feitos à Configuração de Aplicações?

As lojas de configuração no nível Livre estão limitadas a 1.000 pedidos por dia. As lojas de configuração no nível Standard podem sofrer um estrangulamento temporário quando a taxa de pedido exceder 20.000 pedidos por hora.

Quando uma loja atingir o seu limite, devolverá o código de estado HTTP 429 para todos os pedidos feitos até ao termo do prazo. O `retry-after-ms` cabeçalho na resposta dá um tempo de espera sugerido (em milissegundos) antes de voltar a tentar o pedido.

Se a sua aplicação experimentar regularmente respostas do código de estado HTTP 429, considere redesenhar o mesmo para reduzir o número de pedidos feitos. Para obter mais informações, consulte [Reduzir os pedidos feitos para configuração de aplicações](./howto-best-practices.md#reduce-requests-made-to-app-configuration)

## <a name="my-application-receives-http-status-code-429-responses-why"></a>A minha aplicação recebe respostas do código de estado HTTP 429. Porquê?

Receberá uma resposta http status code 429 nestas circunstâncias:

* Excedendo o limite de pedido diário para uma loja no nível Livre.
* Estrangulamento temporário devido a uma alta taxa de pedido para uma loja no nível Standard.
* Uso excessivo da largura de banda.
* Tentar criar ou modificar uma chave quando a cotação de armazenamento for excedida.

Verifique o corpo da resposta 429 pela razão específica pela qual o pedido falhou.

## <a name="how-can-i-receive-announcements-on-new-releases-and-other-information-related-to-app-configuration"></a>Como posso receber anúncios sobre novas versões e outras informações relacionadas com a Configuração de Aplicações?

Subscreva os [nossos anúncios GitHub repo](https://github.com/Azure/AppConfiguration-Announcements).

## <a name="how-can-i-report-an-issue-or-give-a-suggestion"></a>Como posso reportar um problema ou dar uma sugestão?

Pode contactar-nos diretamente no [GitHub.](https://github.com/Azure/AppConfiguration/issues)

## <a name="next-steps"></a>Passos seguintes

* [Sobre a Configuração da Aplicação Azure](./overview.md)
