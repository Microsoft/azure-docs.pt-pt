---
title: FAQ de configuração de aplicativo sinuoso
description: Perguntas frequentes sobre configuração de aplicações do Azure
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: lcozzens
ms.openlocfilehash: 25187fd055f40e8b32d840ead2a9c54882446b88
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80348794"
---
# <a name="azure-app-configuration-faq"></a>FAQ de configuração de aplicativo sinuoso

Este artigo responde frequentemente a perguntas sobre a Configuração de Aplicações Azure.

## <a name="how-is-app-configuration-different-from-azure-key-vault"></a>Como é que a configuração da aplicação é diferente do Cofre de Chaves Azure?

A Configuração de Aplicações ajuda os desenvolvedores a gerir as definições de aplicações e a controlar a disponibilidade de funcionalidades. Tem como objetivo simplificar muitas das tarefas de trabalhar com dados de configuração complexos.

Suportes de configuração de aplicações:

- Espaços de nomes hierárquicos
- Rotulagem
- Consultas extensas
- Recuperação de lotes
- Operações de gestão especializadas
- Uma interface de utilizador de gestão de funcionalidades

A configuração da aplicação complementa o Cofre chave, e os dois devem ser usados lado a lado na maioria das implementações de aplicações.

## <a name="should-i-store-secrets-in-app-configuration"></a>Devo guardar segredos na Configuração de Aplicações?

Embora a Configuração de Aplicações forneça uma segurança reforçada, o Key Vault ainda é o melhor local para armazenar segredos de aplicação. A Key Vault fornece encriptação ao nível de hardware, políticas de acesso granular e operações de gestão, tais como rotação de certificados.

Pode criar valores de Configuração de Aplicações que referenciam segredos armazenados no Cofre chave. Para mais informações, consulte [as referências do Cofre de Chaves numa aplicação core ASP.NET](./use-key-vault-references-dotnet-core.md).

## <a name="does-app-configuration-encrypt-my-data"></a>A Configuração de Aplicações encripta os meus dados?

Sim. A Configuração da Aplicação encripta todos os valores-chave que detém e encripta a comunicação da rede. Os nomes e etiquetas-chave são usados como índices para recuperar dados de configuração e não são encriptados.

## <a name="how-is-app-configuration-different-from-azure-app-service-settings"></a>Como é que a configuração da aplicação é diferente das definições do Serviço de Aplicações Azure?

O Serviço de Aplicações Azure permite-lhe definir as definições de aplicações para cada instância do Serviço de Aplicações. Estas definições são passadas como variáveis ambientais para o código de aplicação. Pode associar um cenário a uma ranhura de implantação específica, se quiser. Para mais informações, consulte as definições da [aplicação Configure](/azure/app-service/configure-common#configure-app-settings).

Em contraste, a Configuração de Aplicações Azure permite definir definições que podem ser partilhadas entre várias aplicações. Isto inclui aplicações em execução no App Service, bem como outras plataformas. O seu código de aplicação acede a estas definições através dos fornecedores de configuração para .NET e Java, através do Azure SDK, ou diretamente através de APIs REST.

Também pode importar e exportar definições entre o App Service e a Configuração de Aplicações. Esta capacidade permite-lhe configurar rapidamente uma nova loja de configuração de aplicações com base nas definições existentes do Serviço de Aplicações. Também pode partilhar a configuração com uma aplicação existente que se baseia nas definições do Serviço de Aplicações.

## <a name="are-there-any-size-limitations-on-keys-and-values-stored-in-app-configuration"></a>Existem limitações de tamanho em chaves e valores armazenados na Configuração de Aplicações?

Há um limite de 10 KB para um único item de valor-chave.

## <a name="how-should-i-store-configurations-for-multiple-environments-test-staging-production-and-so-on"></a>Como devo armazenar configurações para múltiplos ambientes (teste, encenação, produção, e assim por diante)?

Controla quem pode aceder à Configuração de Aplicações a um nível por loja. Utilize uma loja separada para cada ambiente que exija permissões diferentes. Esta abordagem proporciona o melhor isolamento de segurança.

Se não necessitar de isolamento de segurança entre ambientes, pode utilizar etiquetas para diferenciar os valores de configuração. [Utilize etiquetas para permitir configurações diferentes para diferentes ambientes,](./howto-labels-aspnet-core.md) fornece um exemplo completo.

## <a name="what-are-the-recommended-ways-to-use-app-configuration"></a>Quais são as formas recomendadas de usar a Configuração de Aplicações?

Ver [as melhores práticas.](./howto-best-practices.md)

## <a name="how-much-does-app-configuration-cost"></a>Quanto custa a Configuração de Aplicações?

Existem dois níveis de preços:

- Escalão gratuito
- Nível padrão.

Se criou uma loja antes da introdução do nível Standard, passou automaticamente para o nível Livre mediante disponibilidade geral. Pode optar por fazer upgrade para o nível Standard ou permanecer no nível Livre.

Não se pode desvalorizar uma loja do nível Standard para o nível Livre. Pode criar uma nova loja no free tier e, em seguida, importar dados de configuração para essa loja.

## <a name="which-app-configuration-tier-should-i-use"></a>Que nível de configuração de aplicativos devo usar?

Ambos os níveis de configuração de aplicações oferecem funcionalidade seleção, incluindo configurações de config, bandeiras de funcionalidades, referências key vault, operações básicas de gestão, métricas e registos.

Seguem-se considerações para a escolha de um nível.

- **Recursos por subscrição**: Um recurso é composto por uma única loja de configuração. Cada subscrição está limitada a uma loja de configuração no nível livre. As subscrições podem ter um número ilimitado de lojas de configuração no nível padrão.
- **Armazenamento por recurso**: No nível livre, cada loja de configuração está limitada a 10 MB de armazenamento. No nível padrão, cada loja de configuração pode utilizar até 1 GB de armazenamento.
- **Histórico chave**: Configuração de aplicações armazena um histórico de todas as alterações feitas às teclas. No nível livre, esta história está armazenada por sete dias. No nível padrão, esta história é armazenada por 30 dias.
- **Pedidos por dia**: As lojas de nível livre estão limitadas a 1.000 pedidos por dia. Uma vez que uma loja atinge 1.000 pedidos, devolverá o código de estado HTTP 429 para todos os pedidos até à meia-noite UTC.

    Para as lojas standard tier, os primeiros 200.000 pedidos por dia estão incluídos no custo diário. Pedidos adicionais são cobrados como excesso de idade.

- Acordo de **nível**de serviço : O nível padrão tem uma sLA de 99,9% de disponibilidade. O nível livre não tem um SLA.
- **Funcionalidades de segurança**: Ambos os níveis incluem funcionalidades básicas de segurança, incluindo encriptação com chaves geridas pela Microsoft, autenticação via HMAC ou Azure Ative Directory, suporte RBAC e identidade gerida. O nível Standard oferece uma funcionalidade de segurança mais avançada, incluindo suporte private link e encriptação com chaves geridas pelo cliente.
- **Custo**: As lojas standard tier têm uma taxa de utilização diária. Há também uma taxa de excesso de idade para pedidos passado a atribuição diária. Não há custo para usar uma loja de nível livre.

## <a name="can-i-upgrade-a-store-from-the-free-tier-to-the-standard-tier-can-i-downgrade-a-store-from-the-standard-tier-to-the-free-tier"></a>Posso atualizar uma loja do nível Free para o nível Standard? Posso desvalorizar uma loja do nível Standard para o nível Livre?

Pode atualizar do nível Free para o nível Standard a qualquer momento.

Não se pode desvalorizar uma loja do nível Standard para o nível Livre. Pode criar uma nova loja no free tier e, em seguida, importar dados de [configuração para essa loja](howto-import-export-data.md).

## <a name="are-there-any-limits-on-the-number-of-requests-made-to-app-configuration"></a>Existem limites para o número de pedidos feitos à Configuração de Aplicações?

As lojas de configuração no nível Livre estão limitadas a 1.000 pedidos por dia. As lojas de configuração no nível Standard podem sofrer estrangulamentos temporários quando a taxa de pedido exceder 20.000 pedidos por hora.

Quando uma loja atingir o seu limite, devolverá o código de estado HTTP 429 para todos os pedidos feitos até ao termo do prazo. O `retry-after-ms` cabeceamento na resposta dá um tempo de espera sugerido (em milissegundos) antes de voltar a tentar o pedido.

Se a sua aplicação experimentar regularmente o código de estado HTTP 429, considere redesenhar para reduzir o número de pedidos feitos. Para mais informações, consulte [Reduzir os pedidos feitos para a Configuração de Aplicações](./howto-best-practices.md#reduce-requests-made-to-app-configuration)

## <a name="my-application-receives-http-status-code-429-responses-why"></a>A minha aplicação recebe respostas http status code 429. Porquê?

Receberá uma resposta do código de estado HTTP 429 nestas circunstâncias:

* Excedendo o limite diário de pedidos de uma loja no free tier.
* Estrangulamento temporário devido a uma elevada taxa de pedido para uma loja no nível Standard.
* Utilização excessiva da largura de banda.
* Tentar criar ou modificar uma chave quando a cotação de armazenamento é ultrapassada.

Verifique o corpo da resposta 429 pela razão específica pela qual o pedido falhou.

## <a name="how-can-i-receive-announcements-on-new-releases-and-other-information-related-to-app-configuration"></a>Como posso receber anúncios sobre novos lançamentos e outras informações relacionadas com a Configuração de Aplicações?

Subscreva o nosso repo de [anúncios GitHub.](https://github.com/Azure/AppConfiguration-Announcements)

## <a name="how-can-i-report-an-issue-or-give-a-suggestion"></a>Como posso relatar um problema ou dar uma sugestão?

Pode contactar-nos diretamente no [GitHub.](https://github.com/Azure/AppConfiguration/issues)

## <a name="next-steps"></a>Passos seguintes

* [Sobre a Configuração da Aplicação Azure](./overview.md)
