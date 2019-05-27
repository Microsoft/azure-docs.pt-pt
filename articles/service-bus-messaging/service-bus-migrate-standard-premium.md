---
title: Migrar espaços de nomes standard do Azure Service Bus existentes para o escalão premium | Documentos da Microsoft
description: Guia para a migração das existentes do Azure Service Bus espaços de nomes standard para premium
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: darosa
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2019
ms.author: aschhab
ms.openlocfilehash: 65c207b4d03e7d156c8c871a3642601fd0489ead
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2019
ms.locfileid: "65991425"
---
# <a name="migrate-existing-azure-service-bus-standard-namespaces-to-the-premium-tier"></a>Migrar espaços de nomes standard do Azure Service Bus existentes para o escalão premium
Anteriormente, o Azure Service Bus oferecido espaços de nomes apenas no escalão standard. Espaços de nomes são instalações de multi-inquilinos, que são otimizadas para baixo débito e de ambientes de programador. O escalão premium oferece recursos dedicados por espaço de nomes para latência previsível e maior débito por um preço fixo. O escalão premium está otimizado para débito elevado e ambientes de produção que necessitam de funcionalidades empresariais adicionais.

Este artigo descreve como migrar espaços de nomes do escalão standard existente para o escalão premium.  

>[!WARNING]
> Destina-se a migração para espaços de nomes padrão do Service Bus ser atualizado para o escalão premium. A ferramenta de migração não suporta a desatualização.

Alguns dos pontos a serem observados: 
- Esta migração destina-se a acontecer no lugar, o que significa que existentes aplicativos de remetente e o receptor **não necessitam de quaisquer alterações ao código ou configuração**. A cadeia de ligação existente será automaticamente ponto para o novo espaço de nomes premium.
- O **premium** deve ter espaço de nomes **não existem entidades** no mesmo para a migração tenha êxito. 
- Todos os **entidades** no espaço de nomes padrão são **copiado** ao espaço de nomes premium durante o processo de migração. 
- Suporta a migração **1.000 entidades por unidade de mensagens** no escalão premium. Para identificar quantas unidades de mensagens, precisa, comece com o número de entidades que tiver no seu espaço de nomes padrão atual. 
- Diretamente, não é possível migrar a partir **escalão básico** ao **escalão premier**, mas pode fazer então indiretamente através da migração do básico a primeira padrão e, em seguida, para o standard para premium no próximo passo.

## <a name="migration-steps"></a>Passos de migração
Algumas condições estão associadas com o processo de migração. Familiarize-se com os seguintes passos para reduzir a possibilidade de erros. Estes passos descrevem o processo de migração e os detalhes passo a passo estão listados nas secções que se seguem.

1. Crie um novo espaço de nomes premium.
1. Emparelhe os espaços de nomes standard e premium entre si.
1. Entidades de sincronização (a cópia de ativação pós-falha) do padrão para o espaço de nomes premium.
1. Consolide a migração.
1. Drenar entidades no espaço de nomes padrão usando o nome de pós-migração do espaço de nomes.
1. Elimine o espaço de nomes standard.

>[!IMPORTANT]
> Após a migração ter sido comprometida, acessar o espaço de nomes padrão antigo e drenar a filas e subscrições. Depois de tem sido drenadas as mensagens, eles podem ser enviados para o novo espaço de nomes premium a ser processado pelos aplicativos do recetor. Depois de tem sido drenadas a filas e subscrições, recomendamos que elimine o espaço de nomes padrão antigo.

### <a name="migrate-by-using-the-azure-cli-or-powershell"></a>Migrar com a CLI do Azure ou o PowerShell

Para migrar o seu espaço de nomes do Service Bus standard para premium ao utilizar a CLI do Azure ou a ferramenta de PowerShell, siga estes passos.

1. Crie um novo espaço de nomes do Service Bus premium. Pode fazer referência a [modelos Azure Resource Manager](service-bus-resource-manager-namespace.md) ou [utilizar o portal do Azure](service-bus-create-namespace-portal.md). Verifique se seleciona **premium** para o **serviceBusSku** parâmetro.

1. Defina as seguintes variáveis de ambiente para simplificar os comandos de migração.
   ```azurecli
   resourceGroup = <resource group for the standard namespace>
   standardNamespace = <standard namespace to migrate>
   premiumNamespaceArmId = <Azure Resource Manager ID of the premium namespace to migrate to>
   postMigrationDnsName = <post migration DNS name entry to access the standard namespace>
   ```

    >[!IMPORTANT]
    > Pós-migração/nome de alias (post_migration_dns_name) será utilizado para aceder a migração de postagem antigo do espaço de nomes standard. Utilize esta opção para drenar as filas e das subscrições e, em seguida, elimine o espaço de nomes.

1. Emparelhe os espaços de nomes standard e premium e iniciar a sincronização com o seguinte comando:

    ```azurecli
    az servicebus migration start --resource-group $resourceGroup --name $standardNamespace --target-namespace $premiumNamespaceArmId --post-migration-name $postMigrationDnsName
    ```


1. Verifique o estado da migração, com o seguinte comando:
    ```azurecli
    az servicebus migration show --resource-group $resourceGroup --name $standardNamespace
    ```

    A migração será considerada concluída quando vir os seguintes valores:
    * MigrationState = "Ativo"
    * pendingReplicationsOperationsCount = 0
    * provisioningState = "Com êxito"

    Este comando também apresenta a configuração de migração. Verifique se que os valores estão definidos corretamente. Também verificar o espaço de nomes premium no portal para garantir que todas as filas e tópicos foram criados e que correspondem o que existia no espaço de nomes standard.

1. Consolide a migração, executando o seguinte comando completado:
   ```azurecli
   az servicebus migration complete --resource-group $resourceGroup --name $standardNamespace
   ```

### <a name="migrate-by-using-the-azure-portal"></a>Migrar com o portal do Azure

Migração com o portal do Azure tem o mesmo fluxo lógico como migrar com os comandos. Siga estes passos para migrar com o portal do Azure.

1. Sobre o **navegação** menu no painel esquerdo, selecione **para migrar para o premium**. Clique nas **começar** botão para continuar para a página seguinte.
    ![Página de destino de migração][]

1. Concluída **configuração**.
   ![Espaço de nomes de configuração][]
   1. Crie e atribua o espaço de nomes premium para migrar o espaço de nomes padrão existente para.
        ![Configurar o espaço de nomes - criar o espaço de nomes premium][]
   1. Escolher uma **nome após migração**. Irá utilizar este nome para o espaço de nomes padrão de acesso após a migração estar concluída.
        ![Configurar o espaço de nomes - escolher o nome de migração de postagem][]
   1. Selecione **'Seguinte'** para continuar.
1. Entidades de sincronização entre os espaços de nomes standard e premium.
    ![Configurar o espaço de nomes - entidades de sincronização - início][]

   1. Selecione **Iniciar sincronização** para iniciar a sincronização de entidades.
   1. Selecione **Sim** na caixa de diálogo para confirmar e iniciar a sincronização.
   1. Aguarde até que a sincronização seja concluída. O estado está disponível na barra de status.
        ![Espaço de nomes - entidades de sincronização - progresso da configuração][]
        >[!IMPORTANT]
        > Se precisar de abortar a migração por qualquer motivo, consulte o fluxo de abortar na secção de FAQ deste documento.
   1. Depois da sincronização seja concluída, selecione **seguinte** na parte inferior da página.

1. Reveja as alterações na página de resumo. Selecione **concluir a migração** para mudar de espaços de nomes e para concluir a migração.
    ![Alternar menu de comutador - espaço de nomes][] será exibida a página de confirmação quando a migração estiver concluída.
    ![Espaço de nomes do comutador - êxito][]

## <a name="faqs"></a>FAQs

### <a name="what-happens-when-the-migration-is-committed"></a>O que acontece quando a migração está empenhada?

Após a migração está comprometida, a cadeia de ligação que apontava para o espaço de nomes standard irá apontar para o espaço de nomes premium.

Os aplicativos de remetente e o receptor serão desligar do espaço de nomes padrão e voltar a ligar ao espaço de nomes premium automaticamente.

### <a name="what-do-i-do-after-the-standard-to-premium-migration-is-complete"></a>O que fazer posso fazer depois de concluída a norma para a migração do premium?

O padrão para a migração premium garante que os metadados de entidade como tópicos, subscrições e filtros são copiados do namespace padrão para o espaço de nomes premium. Os dados da mensagem que foi consolidados para o espaço de nomes padrão não são copiados do namespace padrão para o espaço de nomes premium.

O espaço de nomes standard pode ter algumas mensagens que foram enviadas e consolidadas enquanto a migração estava em curso. Drenar manualmente estas mensagens do espaço de nomes padrão e enviá-los manualmente para o espaço de nomes de premium. Para drenar manualmente as mensagens, utilize uma aplicação de consola ou de um script que drena as entidades de espaço de nomes padrão usando o nome de DNS de migração de postagem que especificou nos comandos de migração. Envie essas mensagens para o espaço de nomes premium, para que eles podem ser processados pelos recetores.

Depois de tem sido drenadas as mensagens, elimine o espaço de nomes standard.

>[!IMPORTANT]
> Depois de tem sido drenadas as mensagens do espaço de nomes standard, elimine o espaço de nomes standard. Isso é importante porque a cadeia de ligação que inicialmente chamado de espaço de nomes standard agora refere-se ao espaço de nomes premium. Não precisará mais o espaço de nomes standard. A eliminar o espaço de nomes padrão que migrou ajuda a reduzir a confusão posterior.

### <a name="how-much-downtime-do-i-expect"></a>Quanto tempo de inatividade posso esperar?
O processo de migração destina-se para reduzir o tempo de inatividade esperado para as aplicações. Tempo de inatividade é reduzido utilizando a cadeia de ligação que os aplicativos de remetente e o receptor utilizam para apontar para o novo espaço de nomes premium.

O período de indisponibilidade ocorre pela aplicação está limitado ao tempo que demora a atualizar a entrada DNS para apontar para o espaço de nomes premium. Tempo de inatividade é de aproximadamente 5 minutos.

### <a name="do-i-have-to-make-any-configuration-changes-while-doing-the-migration"></a>É necessário efetuar quaisquer alterações de configuração ao fazer a migração?
Não, não há nenhuma alteração de configuração ou de código necessária para fazer a migração. A cadeia de ligação que os aplicativos de remetente e o receptor usar para acessar o Namespace padrão é automaticamente mapeada para atuar como um alias de espaço de nomes premium.

### <a name="what-happens-when-i-abort-the-migration"></a>O que acontece quando eu abortar a migração?
A migração pode ser anulada, utilizando o `Abort` comando ou através da utilização do portal do Azure. 

#### <a name="azure-cli"></a>CLI do Azure

```azurecli
az servicebus migration abort --resource-group $resourceGroup --name $standardNamespace
```

#### <a name="azure-portal"></a>Portal do Azure

![Abortar fluxo - sincronização de abortar][]
![abortar fluxo - abortar concluída][]

Quando o processo de migração é abortado, ele anulará o processo de copiar as entidades (tópicos, subscrições e filtros) do padrão para o espaço de nomes premium e interrompe o emparelhamento.

A cadeia de ligação não é atualizada para apontar para o espaço de nomes premium. Os aplicativos existentes continuam a funcionar como antes de iniciar a migração.

No entanto, ela não eliminar as entidades no espaço de nomes premium ou eliminar o espaço de nomes premium. Elimine manualmente as entidades, se decidiu não seguir em frente com a migração.

>[!IMPORTANT]
> Se optar por abortar a migração, elimine o espaço de nomes que tinha aprovisionado para a migração para que não lhe é cobrados os recursos de premium.

#### <a name="i-dont-want-to-have-to-drain-the-messages-what-do-i-do"></a>Não quero ter drenar as mensagens. O que posso fazer?

Pode haver mensagens enviadas pelos aplicativos remetente e dedicadas para o armazenamento no espaço de nomes standard, enquanto a migração está a ocorrer e antes da migração está comprometida.

Durante a migração, o mensagem real/payload de dados não é copiado do padrão para o espaço de nomes premium. As mensagens tem de ser drenados manualmente e, em seguida, enviado para o espaço de nomes premium.

No entanto, se é possível migrar durante uma janela de manutenção planeada/manutenção e não deseja drenar e enviar as mensagens manualmente, siga estes passos:

1. Pare os aplicativos do remetente. Os aplicativos de recetor processará as mensagens que estão atualmente no espaço de nomes padrão e vão drenar a fila.
1. Depois da filas e subscrições no espaço de nomes padrão estiverem vazias, siga o procedimento descrito anteriormente para executar a migração do padrão para o espaço de nomes premium.
1. Após a migração estar concluída, pode reiniciar os aplicativos do remetente.
1. Os remetentes e recetores agora irão ligar automaticamente com o espaço de nomes premium.

    >[!NOTE]
    > Não é necessário que parar os aplicativos de recetor para a migração.
    >
    > Após a migração estar concluída, os aplicativos de recetor irão desligar do espaço de nomes padrão e ligar automaticamente ao espaço de nomes premium.

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre o [diferenças entre o serviço de mensagens standard e premium](./service-bus-premium-messaging.md).
* Saiba mais sobre o [aspectos de recuperação após desastre geográfico e com elevada disponibilidade para o premium do Service Bus](service-bus-outages-disasters.md#protecting-against-outages-and-disasters---service-bus-premium).

[Página de destino de migração]: ./media/service-bus-standard-premium-migration/1.png
[Espaço de nomes de configuração]: ./media/service-bus-standard-premium-migration/2.png
[Configurar o espaço de nomes - criar o espaço de nomes premium]: ./media/service-bus-standard-premium-migration/3.png
[Configurar o espaço de nomes - escolher o nome de migração de postagem]: ./media/service-bus-standard-premium-migration/4.png
[Configurar o espaço de nomes - entidades de sincronização - início]: ./media/service-bus-standard-premium-migration/5.png
[Espaço de nomes - entidades de sincronização - progresso da configuração]: ./media/service-bus-standard-premium-migration/8.png
[Espaço de nomes do comutador - menu de comutador]: ./media/service-bus-standard-premium-migration/9.png
[Espaço de nomes do comutador - êxito]: ./media/service-bus-standard-premium-migration/12.png

[Abortar fluxo - abortar a sincronização]: ./media/service-bus-standard-premium-migration/abort1.png
[Abortar fluxo - abortar concluída]: ./media/service-bus-standard-premium-migration/abort3.png
