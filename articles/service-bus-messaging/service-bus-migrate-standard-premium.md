---
title: Migrar espaços de nomes de autocarros do Serviço Azure - standard to premium
description: Guia para permitir a migração dos espaços de nome padrão do Azure Service Bus para o prémio
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 1ed09a077f086390c658e6650171c552b361008d
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "85340751"
---
# <a name="migrate-existing-azure-service-bus-standard-namespaces-to-the-premium-tier"></a>Migrar espaços de nome padrão Azure Service Bus existentes para o nível premium

Anteriormente, a Azure Service Bus oferecia espaços de nome apenas no nível padrão. Os espaços de nome são configurações multi-inquilinos que são otimizadas para ambientes de baixa produção e desenvolvedores. O nível premium oferece recursos dedicados por espaço de nome para uma latência previsível e aumento da produção a um preço fixo. O nível premium é otimizado para ambientes de alta produção e produção que requerem características adicionais da empresa.

Este artigo descreve como migrar os espaços de nome padrão existentes para o nível premium.  

>[!WARNING]
> A migração destina-se a que os espaços de nome padrão do Service Bus sejam atualizados para o nível premium. A ferramenta de migração não suporta a degradação.

Alguns dos pontos a notar:

- Esta migração deve acontecer, o que significa que as aplicações existentes de remetente e recetor **não requerem alterações ao código ou configuração**. A cadeia de ligação existente apontará automaticamente para o novo espaço de nome premium.
- O espaço de nome **premium** não deve ter **entidades** para que a migração tenha sucesso.
- Todas as **entidades** do espaço de nome padrão são **copiadas** para o espaço de nome premium durante o processo de migração.
- A migração apoia **1.000 entidades por unidade de mensagens** no nível premium. Para identificar quantas unidades de mensagens precisa, comece pelo número de entidades que tem no seu espaço de nome padrão atual.
- Não pode migrar diretamente do **nível básico** para **o nível premium,** mas pode fazê-lo indiretamente migrando de base para padrão primeiro e depois do padrão para o prémio no passo seguinte.

## <a name="migration-steps"></a>Passos de migração

Algumas condições estão associadas ao processo de migração. Familiarize-se com os seguintes passos para reduzir a possibilidade de erros. Estes passos descrevem o processo de migração, e os detalhes passo a passo estão listados nas secções que se seguem.

1. Crie um novo espaço de nome premium.
1. Emparelhar os espaços de nome padrão e premium entre si.
1. Sync (copy-over) entidades do padrão para o espaço de nome premium.
1. Cometa a migração.
1. Entidades de drenagem no espaço de nome padrão usando o nome pós-migração do espaço de nome.
1. Elimine o espaço de nomes padrão.

>[!IMPORTANT]
> Após a migração ter sido comprometida, aceda ao antigo espaço de nome padrão e drene as filas e subscrições. Depois de as mensagens terem sido drenadas, podem ser enviadas para o novo espaço de nome premium para serem processadas pelas aplicações do recetor. Depois de esgotadas as filas e as subscrições, recomendamos que apague o antigo espaço de nome padrão.

### <a name="migrate-by-using-the-azure-cli-or-powershell"></a>Migrar usando o Azure CLI ou PowerShell

Para migrar o seu espaço de nome padrão service bus para o prémio utilizando a ferramenta Azure CLI ou PowerShell, siga estes passos.

1. Crie um novo espaço de nome premium service bus. Pode fazer referência aos [modelos do Gestor de Recursos Azure](service-bus-resource-manager-namespace.md) ou [utilizar o portal Azure](service-bus-create-namespace-portal.md). Certifique-se de selecionar **o prémio** para o **parâmetro ServiceBusSku.**

1. Desaprote as seguintes variáveis ambientais para simplificar os comandos de migração.

   ```
   resourceGroup = <resource group for the standard namespace>
   standardNamespace = <standard namespace to migrate>
   premiumNamespaceArmId = <Azure Resource Manager ID of the premium namespace to migrate to>
   postMigrationDnsName = <post migration DNS name entry to access the standard namespace>
   ```

    >[!IMPORTANT]
    > O pseudónimo/nome pós-migração (post_migration_dns_name) será utilizado para aceder à migração de um espaço de nome padrão antigo. Utilize isto para drenar as filas e as subscrições e, em seguida, apagar o espaço de nomes.

1. Emparelhe os espaços de nome standard e premium e inicie a sincronização utilizando o seguinte comando:

    ```azurecli-interactive
    az servicebus migration start --resource-group $resourceGroup --name $standardNamespace --target-namespace $premiumNamespaceArmId --post-migration-name $postMigrationDnsName
    ```

1. Verifique o estado da migração utilizando o seguinte comando:

    ```azurecli-interactive
    az servicebus migration show --resource-group $resourceGroup --name $standardNamespace
    ```

    A migração é considerada completa quando se vê os seguintes valores:

    * Estado de Migração = "Ativo"
    * pendentesReplicaçõesOperações Contas = 0
    * provisionamento Estado = "Bem sucedido"

    Este comando também mostra a configuração de migração. Verifique se os valores estão corretamente definidos. Verifique também o espaço de nome premium no portal para garantir que todas as filas e tópicos foram criados, e que correspondem ao que existia no espaço de nome padrão.

1. Cometer a migração executando o seguinte comando completo:

   ```azurecli-interactive
   az servicebus migration complete --resource-group $resourceGroup --name $standardNamespace
   ```

### <a name="migrate-by-using-the-azure-portal"></a>Migrar usando o portal Azure

A migração utilizando o portal Azure tem o mesmo fluxo lógico que migrar usando os comandos. Siga estes passos para migrar utilizando o portal Azure.

1. No menu **Navegação** no painel esquerdo, **selecione Migrar para o prémio**. Clique no botão **Iniciar** para continuar para a página seguinte.
    ![Página de desembarque de migração][]

1. **Configuração**completa .
   ![Espaço de nome de configuração][]
   1. Crie e atribua o espaço de nome premium para migrar o espaço de nome padrão existente para.
        ![Setup namespace - crie espaço de nome premium][]
   1. Escolha um **nome pós-migração.** Você usará este nome para aceder ao espaço de nome padrão após a migração estar completa.
        ![Setup namespace - escolha o nome da migração pós-migração][]
   1. Selecione **'Seguinte'** para continuar.
1. Sync entidades entre os espaços de nome standard e premium.
    ![Espaço de nome de configuração - entidades sincronizadas - iniciar][]

   1. Selecione **Start Sync** para começar a sincronizar as entidades.
   1. Selecione **Sim** na caixa de diálogo para confirmar e iniciar a sincronização.
   1. Espere até que a sincronização esteja completa. O estado está disponível na barra de estado.
        ![Espaço de nome de configuração - entidades sincronizadas - progresso][]
        >[!IMPORTANT]
        > Se precisar de abortar a migração por qualquer motivo, por favor reveja o fluxo de abortar na secção de PERGUNTAS Frequentes deste documento.
   1. Depois de concluída a sincronização, selecione **Seguinte** na parte inferior da página.

1. Reveja as alterações na página do resumo. Selecione **A Migração Completa** para mudar os espaços de nome e para completar a migração.
    ![Altere o espaço de nomes - menu de comutação][]  
    A página de confirmação aparece quando a migração está completa.
    ![Switch namespace - sucesso][]

## <a name="caveats"></a>Ressalvas

Algumas das funcionalidades fornecidas pela Azure Service Bus Standard não são suportadas pelo nível Azure Service Bus Premium. Estes são por design, uma vez que o nível premium oferece recursos dedicados para produção e latência previsíveis.

Aqui está uma lista de funcionalidades não suportadas pela Premium e a sua mitigação -

### <a name="express-entities"></a>Entidades expressas

   As entidades expressas que não comprometam quaisquer dados de mensagens para armazenamento não são suportadas no Premium. Os recursos dedicados proporcionaram uma melhoria significativa da produção, garantindo que os dados são persistidos, como é esperado por qualquer sistema de mensagens empresariais.

   Durante a migração, qualquer uma das suas entidades expressas no seu espaço de nome Standard será criada no espaço de nome Premium como entidade não expressa.

   Se utilizar os modelos Azure Resource Manager (ARM), certifique-se de que remove a bandeira 'activarExpress' da configuração de implementação para que os seus fluxos de trabalho automatizados executem sem erros.

### <a name="partitioned-entities"></a>Entidades particionadas

   As entidades divididas foram apoiadas no nível Standard para proporcionar uma melhor disponibilidade numa configuração multi-arrendatário. Com a disponibilização de recursos dedicados disponíveis por espaço de nome no nível Premium, isso já não é necessário.

   Durante a migração, qualquer entidade dividida no espaço de nome padrão é criada no espaço de nome Premium como uma entidade não dividida.

   Se o seu modelo ARM definir 'enablePartitioning' para 'true' para uma determinada fila ou tópico, então será ignorado pelo corretor.

## <a name="faqs"></a>FAQs

### <a name="what-happens-when-the-migration-is-committed"></a>O que acontece quando a migração é comprometida?

Após a migração ser comprometida, a cadeia de ligação que apontava para o espaço de nome padrão apontará para o espaço de nome premium.

As aplicações de remetente e recetor desligam-se do espaço namespace padrão e reconectar-se-ão automaticamente ao espaço de nome premium.

### <a name="what-do-i-do-after-the-standard-to-premium-migration-is-complete"></a>O que faço depois de concluída a norma para a migração premium?

A migração padrão para a migração premium garante que os metadados da entidade, tais como tópicos, subscrições e filtros são copiados do espaço de nome padrão para o espaço de nome premium. Os dados de mensagens que foram comprometidos com o espaço de nome padrão não são copiados do espaço de nome padrão para o espaço de nome premium.

O espaço de nome padrão pode ter algumas mensagens que foram enviadas e cometidas enquanto a migração estava em curso. Drene manualmente estas mensagens do espaço namespace padrão e envie-as manualmente para o espaço de nome premium. Para drenar manualmente as mensagens, utilize uma aplicação de consola ou um script que drene as entidades padrão do espaço de nome utilizando o nome DNS pós-migração que especificou nos comandos de migração. Envie estas mensagens para o espaço de nome premium para que possam ser processadas pelos recetores.

Depois de esvaziadas as mensagens, elimine o espaço de nome padrão.

>[!IMPORTANT]
> Depois de esvaziadas as mensagens do espaço de nome padrão, elimine o espaço de nome padrão. Isto é importante porque a cadeia de ligação que inicialmente se referiu ao espaço de nome padrão refere-se agora ao espaço de nome premium. Não vai mais precisar do espaço padrão. Eliminar o espaço de nome padrão que emigrou ajuda a reduzir a confusão posterior.

### <a name="how-much-downtime-do-i-expect"></a>Quanto tempo de descanso espero?

O processo de migração destina-se a reduzir o tempo de inatividade esperado para as aplicações. O tempo de inatividade é reduzido utilizando a cadeia de ligação que as aplicações do remetente e do recetor usam para apontar para o novo espaço de nome premium.

O tempo de inatividade que é experimentado pela aplicação limita-se ao tempo que leva para atualizar a entrada de DNS para apontar para o espaço de nome premium. O tempo de inatividade é de aproximadamente 5 minutos.

### <a name="do-i-have-to-make-any-configuration-changes-while-doing-the-migration"></a>Tenho de fazer alterações de configuração durante a migração?

Não, não há alterações de código ou configuração necessárias para fazer a migração. A cadeia de ligação que as aplicações de remetente e recetor usam para aceder ao Espaço Name padrão é automaticamente mapeada para funcionar como um pseudónimo para o espaço de nome premium.

### <a name="what-happens-when-i-abort-the-migration"></a>O que acontece quando abortar a migração?

A migração pode ser abortada através do `Abort` comando ou através do portal Azure.

#### <a name="azure-cli"></a>CLI do Azure

```azurecli-interactive
az servicebus migration abort --resource-group $resourceGroup --name $standardNamespace
```

#### <a name="azure-portal"></a>Portal do Azure

![Abortar flow - abortar sincronizar ][]
 ![ fluxo de abortar - abortar completa][]

Quando o processo de migração é abortado, aborta o processo de cópia das entidades (tópicos, subscrições e filtros) do padrão para o espaço de nome premium e quebra o emparelhamento.

A cadeia de ligação não é atualizada para apontar para o espaço de nome premium. As suas aplicações existentes continuam a funcionar como antes de iniciar a migração.

No entanto, não elimina as entidades no espaço de nome premium ou apaga o espaço de nome premium. Elimine as entidades manualmente se decidir não avançar com a migração.

>[!IMPORTANT]
> Se decidir abortar a migração, elimine o espaço namespace premium que tinha previsto para a migração para que não seja cobrado pelos recursos.

#### <a name="i-dont-want-to-have-to-drain-the-messages-what-do-i-do"></a>Não quero ter de drenar as mensagens. O que posso fazer?

Pode haver mensagens que são enviadas pelas aplicações do remetente e comprometidas com o armazenamento no espaço padrão namespace enquanto a migração está a decorrer e pouco antes da migração ser comprometida.

Durante a migração, os dados de mensagens/carga útil reais não são copiados do padrão para o espaço de nome premium. As mensagens têm de ser drenadas manualmente e depois enviadas para o espaço de nome premium.

No entanto, se puder migrar durante uma janela de manutenção/limpeza planeada, e não quiser drenar manualmente e enviar as mensagens, siga estes passos:

1. Pare as aplicações de remetente. As aplicações do recetor processarão as mensagens que estão atualmente no espaço de nome padrão e irão drenar a fila.
1. Depois de esvaziar as filas e subscrições no Namespace padrão, siga o procedimento descrito anteriormente para executar a migração do padrão para o espaço de nome premium.
1. Após a migração estar concluída, pode reiniciar as aplicações do remetente.
1. Os remetentes e recetores ligar-se-ão automaticamente ao espaço de nome premium.

    >[!NOTE]
    > Não é preciso parar as aplicações do recetor para a migração.
    >
    > Após a migração estar concluída, as aplicações do recetor desligar-se-ão do espaço de nome padrão e ligar-se-ão automaticamente ao espaço de nomes premium.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre as [diferenças entre mensagens standard e premium.](./service-bus-premium-messaging.md)
* Conheça os [aspetos de alta disponibilidade e recuperação Geo-Disaster para o prémio service bus.](service-bus-outages-disasters.md#protecting-against-outages-and-disasters---service-bus-premium)

[Página de desembarque de migração]: ./media/service-bus-standard-premium-migration/1.png
[Espaço de nome de configuração]: ./media/service-bus-standard-premium-migration/2.png
[Setup namespace - crie espaço de nome premium]: ./media/service-bus-standard-premium-migration/3.png
[Setup namespace - escolha o nome da migração pós-migração]: ./media/service-bus-standard-premium-migration/4.png
[Espaço de nome de configuração - entidades sincronizadas - iniciar]: ./media/service-bus-standard-premium-migration/5.png
[Espaço de nome de configuração - entidades sincronizadas - progresso]: ./media/service-bus-standard-premium-migration/8.png
[Altere o espaço de nomes - menu de comutação]: ./media/service-bus-standard-premium-migration/9.png
[Switch namespace - sucesso]: ./media/service-bus-standard-premium-migration/12.png

[Abortar fluxo - abortar sincronização]: ./media/service-bus-standard-premium-migration/abort1.png
[Abortar flow - abortar completo]: ./media/service-bus-standard-premium-migration/abort3.png
