---
title: Espaços de nome sinuosos do ônibus do serviço Migrate Azure - padrão para premium
description: Guia para permitir a migração dos espaços padrão do Azure Service Bus existentes para o premium
services: service-bus-messaging
documentationcenter: ''
author: axisc
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2019
ms.author: aschhab
ms.openlocfilehash: 548163f4c86f4df4d858b31afd95e0e4615f1696
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77587503"
---
# <a name="migrate-existing-azure-service-bus-standard-namespaces-to-the-premium-tier"></a>Migrar os espaços padrão do Ônibus de serviço azure existentes para o nível premium
Anteriormente, o Azure Service Bus oferecia espaços com nomes apenas no nível padrão. Espaços de nome são configurações multi-inquilinos que são otimizadas para ambientes de baixa entrada e desenvolvedores. O nível premium oferece recursos dedicados por espaço de nome para latência previsível e aumento da contribuição a um preço fixo. O nível premium está otimizado para ambientes de alta produção e produção que requerem funcionalidades adicionais da empresa.

Este artigo descreve como migrar os espaços de nome padrão existentes para o nível premium.  

>[!WARNING]
> A migração destina-se a que os espaços de nome padrão service Bus sejam atualizados para o nível premium. A ferramenta de migração não suporta a degradação.

Alguns dos pontos a notar: 
- Esta migração deve acontecer no lugar, o que significa que as aplicações existentes para o remetente e para o recetor **não requerem alterações ao código ou configuração**. A cadeia de ligação existente apontará automaticamente para o novo espaço de nome premium.
- O espaço de nome **premium** não deve ter **entidades** nele para que a migração tenha sucesso. 
- Todas as **entidades** do espaço de nome padrão são **copiadas** para o espaço de nome premium durante o processo de migração. 
- A migração suporta **1.000 entidades por unidade de mensagens** no escalão premium. Para identificar quantas unidades de mensagens precisa, comece pelo número de entidades que tem no seu espaço de nome padrão atual. 
- Não se pode migrar diretamente do **nível básico** para **o nível premium,** mas pode fazê-lo indiretamente migrando do básico para o padrão primeiro e depois do padrão para o prémio no próximo passo.

## <a name="migration-steps"></a>Passos de migração
Algumas condições estão associadas ao processo de migração. Familiarize-se com os seguintes passos para reduzir a possibilidade de erros. Estes passos delineiam o processo de migração, e os detalhes passo a passo estão listados nas secções que se seguem.

1. Crie um novo espaço de nome premium.
1. Emparelhem os espaços de nome padrão e premium uns com os outros.
1. Sync (copy-over) entidades da norma para o espaço de nome premium.
1. Comprometa a migração.
1. Drenar entidades no espaço de nome padrão utilizando o nome pós-migração do espaço de nome.
1. Apague o espaço de nome padrão.

>[!IMPORTANT]
> Depois da migração ter sido comprometida, aceda ao antigo espaço de nome padrão e drene as filas e subscrições. Depois de as mensagens terem sido drenadas, podem ser enviadas para o novo espaço de nome premium a ser processado pelas aplicações recetoras. Depois de as filas e subscrições terem sido drenadas, recomendamos que apague o antigo espaço de nome padrão.

### <a name="migrate-by-using-the-azure-cli-or-powershell"></a>Migrar utilizando o Azure CLI ou powerShell

Para migrar o seu espaço de nome padrão Service Bus para premium utilizando a ferramenta Azure CLI ou PowerShell, siga estes passos.

1. Crie um novo espaço de nome premium Service Bus. Pode fazer referência aos [modelos do Gestor](service-bus-resource-manager-namespace.md) de Recursos Do Azure ou utilizar o portal [Azure](service-bus-create-namespace-portal.md). Certifique-se de selecionar **prémio** para o parâmetro de **serviçoBusSku.**

1. Desconte as seguintes variáveis ambientais para simplificar os comandos de migração.
   ```azurecli
   resourceGroup = <resource group for the standard namespace>
   standardNamespace = <standard namespace to migrate>
   premiumNamespaceArmId = <Azure Resource Manager ID of the premium namespace to migrate to>
   postMigrationDnsName = <post migration DNS name entry to access the standard namespace>
   ```

    >[!IMPORTANT]
    > O pseudónimo/nome pós-migração (post_migration_dns_name) será utilizado para aceder ao antigo espaço de identificação padrão pós-migração. Use isto para drenar as filas e as subscrições e, em seguida, apagar o espaço de nome.

1. Emparelhe os espaços de nome padrão e premium e inicie a sincronização utilizando o seguinte comando:

    ```azurecli
    az servicebus migration start --resource-group $resourceGroup --name $standardNamespace --target-namespace $premiumNamespaceArmId --post-migration-name $postMigrationDnsName
    ```


1. Verifique o estado da migração utilizando o seguinte comando:
    ```azurecli
    az servicebus migration show --resource-group $resourceGroup --name $standardNamespace
    ```

    A migração é considerada completa quando se vê os seguintes valores:
    * MigrationState = "Ativo"
    * pendingReplicationsOperationsCount = 0
    * provisionamentoEstado = "Bem sucedido"

    Este comando também exibe a configuração da migração. Verifique se os valores estão corretamente definidos. Verifique também o espaço de nome premium no portal para garantir que todas as filas e tópicos foram criados, e que correspondem ao que existia no espaço de nome padrão.

1. Comprometa a migração executando o seguinte comando completo:
   ```azurecli
   az servicebus migration complete --resource-group $resourceGroup --name $standardNamespace
   ```

### <a name="migrate-by-using-the-azure-portal"></a>Migrar usando o portal Azure

A migração utilizando o portal Azure tem o mesmo fluxo lógico que migrar utilizando os comandos. Siga estes passos para migrar utilizando o portal Azure.

1. No menu **Navegação** no painel esquerdo, **selecione Migrar para premium**. Clique no botão **Get Started** para continuar na página seguinte.
    ![página de aterragem da migração][]

1. **Configuração**completa .
   ![configuração do espaço de espaço][]
   1. Crie e atribua o espaço de nome premium para migrar o espaço de nome padrão existente para.
        ![espaço de nome configuração - crie espaço de nome premium][]
   1. Escolha um **nome pós-migração**. Usará este nome para aceder ao espaço de nome padrão após a migração estar completa.
        ![espaço de nome configuração - escolha o nome pós-migração][]
   1. Selecione **'Next'** para continuar.
1. Sincronizar entidades entre os espaços de nome padrão e premium.
    ![Espaço de nome de configuração - entidades de sincronização - iniciar][]

   1. Selecione **Iniciar Sincronização** para começar a sincronizar as entidades.
   1. Selecione **Sim** na caixa de diálogo para confirmar e iniciar a sincronização.
   1. Espere até que a sincronização esteja completa. O estado está disponível na barra de estado.
        ![espaço de nome de configuração - entidades de sincronização -][] de progresso
        >[!IMPORTANT]
        > Se precisar abortar a migração por qualquer motivo, por favor reveja o fluxo de abortar na secção DE PERGUNTAS FREQUENTEs deste documento.
   1. Depois da sincronização estar completa, selecione **Seguinte** na parte inferior da página.

1. Rever alterações na página de resumo. Selecione **Complete Migration** para mudar de espaço de nomes e para completar a migração.
    ![Espaço de nome Switch - menu de comutação][]  
    A página de confirmação aparece quando a migração está completa.
    ![Espaço de nome Switch -][] de sucesso

## <a name="caveats"></a>Ressalvas

Algumas das funcionalidades fornecidas pelo nível Azure Service Bus Standard não são suportadas pelo nível Azure Service Bus Premium. Estes são por design, uma vez que o nível premium oferece recursos dedicados para a entrada e latência previsíveis.

Aqui está uma lista de funcionalidades não suportadas pelo Premium e sua mitigação - 

### <a name="express-entities"></a>Entidades expressas

   Entidades expressas que não comprometam quaisquer dados de mensagens ao armazenamento não são suportadas no Premium. Os recursos dedicados proporcionaram uma melhoria significativa do seu contributo, garantindo que os dados são persistidos, como é esperado de qualquer sistema de mensagens empresariais.
   
   Durante a migração, qualquer uma das suas entidades expressas no seu espaço de nome Standard será criada no espaço de nome Premium como uma entidade não expressa.
   
   Se utilizar os modelos do Gestor de Recursos Azure (ARM), certifique-se de que remove a bandeira 'enableExpress' da configuração de implementação para que os seus fluxos de trabalho automatizados executem sem erros.

### <a name="partitioned-entities"></a>Entidades particionadas

   As entidades divididas foram apoiadas no nível Standard para proporcionar uma melhor alablilidade numa configuração multi-arrendatária. Com a disponibilização de recursos dedicados disponíveis por espaço de nome no escalão Premium, isso já não é necessário.
   
   Durante a migração, qualquer entidade dividida no espaço de nome Standard é criada no espaço de nome Premium como uma entidade não dividida.
   
   Se o seu modelo ARM definir 'enablePartitioning' para 'true' para uma fila ou tópico específico, então será ignorado pelo corretor.

## <a name="faqs"></a>Perguntas mais frequentes

### <a name="what-happens-when-the-migration-is-committed"></a>O que acontece quando a migração é comprometida?

Após a migração ser comprometida, a cadeia de ligação que apontava para o espaço de nome padrão apontará para o espaço de nome premium.

As aplicações do remetente e do recetor desligar-se-ão do espaço de nome padrão e voltarão a ligar-se automaticamente ao espaço de nome premium.

### <a name="what-do-i-do-after-the-standard-to-premium-migration-is-complete"></a>O que faço depois de o padrão de migração premium estar completo?

A norma para a migração premium garante que os metadados da entidade, tais como tópicos, subscrições e filtros, são copiados do espaço de nome padrão para o espaço de nome premium. Os dados de mensagem que foram comprometidos com o espaço de nome padrão não são copiados do espaço de nome padrão para o espaço de nome premium.

O espaço-nome padrão pode ter algumas mensagens que foram enviadas e cometidas enquanto a migração estava em curso. Escorra manualmente estas mensagens do espaço de nome padrão e envie-as manualmente para o espaço de nome premium. Para drenar manualmente as mensagens, utilize uma aplicação de consola ou um script que drene as entidades de espaço de nome padrão utilizando o nome DNS pós-migração que especificou nos comandos de migração. Envie estas mensagens para o espaço de nome premium para que possam ser processadas pelos recetores.

Depois de as mensagens terem sido drenadas, elimine o espaço de nome padrão.

>[!IMPORTANT]
> Depois de as mensagens do espaço de nome padrão terem sido drenadas, elimine o espaço de nome padrão. Isto é importante porque a cadeia de ligação que inicialmente se referia ao espaço de nome padrão agora se refere ao espaço de nome premium. Não vai precisar mais do espaço de nome padrão. Apagar o espaço de nome padrão que emigrou ajuda a reduzir a confusão posterior.

### <a name="how-much-downtime-do-i-expect"></a>Quanto tempo de descanso espero?
O processo de migração destina-se a reduzir o tempo de inatividade esperado para as aplicações. O tempo de inatividade é reduzido utilizando a cadeia de ligação que as aplicações do remetente e do recetor utilizam para apontar para o novo espaço de nome premium.

O tempo de inatividade que é experimentado pela aplicação limita-se ao tempo que demora a atualizar a entrada de DNS para apontar para o espaço de nome premium. O tempo de inatividade é de aproximadamente 5 minutos.

### <a name="do-i-have-to-make-any-configuration-changes-while-doing-the-migration"></a>Tenho de fazer alguma mudança de configuração durante a migração?
Não, não há alterações de código ou configuração necessárias para fazer a migração. A cadeia de ligação que as aplicações de remetente e recetor usam para aceder ao espaço nome padrão é automaticamente mapeada para funcionar como um pseudónimo para o espaço de nome premium.

### <a name="what-happens-when-i-abort-the-migration"></a>O que acontece quando aborto a migração?
A migração pode ser abortada usando o comando `Abort` ou utilizando o portal Azure. 

#### <a name="azure-cli"></a>CLI do Azure

```azurecli
az servicebus migration abort --resource-group $resourceGroup --name $standardNamespace
```

#### <a name="azure-portal"></a>Portal do Azure

![Abortar fluxo - abortar sincronização][]
![abortar fluxo - abortar][] completa

Quando o processo de migração é abortado, aborta o processo de cópia das entidades (tópicos, subscrições e filtros) do padrão para o espaço de nome premium e quebra o emparelhamento.

A cadeia de ligação não é atualizada para apontar para o espaço de nome premium. As suas aplicações existentes continuam a funcionar como antes de iniciar a migração.

No entanto, não elimina as entidades no espaço de nome premium nem elimina o espaço de nome premium. Elimine as entidades manualmente se decidiu não avançar com a migração.

>[!IMPORTANT]
> Se decidir abortar a migração, elimine o espaço de nome premium que tinha previsto para a migração para que não seja cobrado pelos recursos.

#### <a name="i-dont-want-to-have-to-drain-the-messages-what-do-i-do"></a>Não quero ter de drenar as mensagens. O que posso fazer?

Pode haver mensagens que são enviadas pelas aplicações do remetente e comprometidas com o armazenamento no espaço nome padrão enquanto a migração está a decorrer e pouco antes da migração ser comprometida.

Durante a migração, os dados reais da mensagem/carga útil não são copiados do padrão para o espaço de nome premium. As mensagens têm de ser drenadas manualmente e depois enviadas para o espaço de nome premium.

No entanto, se conseguir migrar durante uma janela de manutenção/limpeza planeada, e não quiser drenar manualmente e enviar as mensagens, siga estes passos:

1. Parem as aplicações do remetente. As aplicações do recetor processarão as mensagens que estão atualmente no espaço de nome padrão e irão drenar a fila.
1. Depois de as filas e subscrições no espaço nome padrão estarem vazias, siga o procedimento descrito anteriormente para executar a migração da norma para o espaço de nome premium.
1. Após a migração estar concluída, pode reiniciar as aplicações do remetente.
1. Os remetentes e recetores ligar-se-ão automaticamente ao espaço de nome premium.

    >[!NOTE]
    > Não é preciso parar as aplicações de recetores para a migração.
    >
    > Após a migração estar concluída, as aplicações do recetor desligar-se-ão do espaço de nome padrão e ligar-se-ão automaticamente ao espaço de nome premium.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre as [diferenças entre mensagens padrão e premium.](./service-bus-premium-messaging.md)
* Conheça os [aspetos de alta disponibilidade e recuperação de desastres para o prémio service bus.](service-bus-outages-disasters.md#protecting-against-outages-and-disasters---service-bus-premium)

[Página de aterragem da migração]: ./media/service-bus-standard-premium-migration/1.png
[Espaço de nome de configuração]: ./media/service-bus-standard-premium-migration/2.png
[Configurar espaço de nome - crie espaço de nome premium]: ./media/service-bus-standard-premium-migration/3.png
[Configurar espaço de nome - escolha o nome pós-migração]: ./media/service-bus-standard-premium-migration/4.png
[Configurar espaço de nome - entidades de sincronização - início]: ./media/service-bus-standard-premium-migration/5.png
[Configurar espaço de nome - entidades sincronizadas - progresso]: ./media/service-bus-standard-premium-migration/8.png
[Mudar espaço de nome - comutador menu]: ./media/service-bus-standard-premium-migration/9.png
[Mudar espaço de nome - sucesso]: ./media/service-bus-standard-premium-migration/12.png

[Abortar fluxo - abortar sincronização]: ./media/service-bus-standard-premium-migration/abort1.png
[Abortar fluxo - abortar completo]: ./media/service-bus-standard-premium-migration/abort3.png
