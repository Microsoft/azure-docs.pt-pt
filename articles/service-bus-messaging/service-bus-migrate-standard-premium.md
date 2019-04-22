---
title: Migrar existente do Azure Service Bus espaços de nomes padrão para o escalão Premium | Documentos da Microsoft
description: Guia para a migração das existentes do Azure Service Bus espaços de nomes Standard para Premium
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
ms.date: 02/18/2019
ms.author: aschhab
ms.openlocfilehash: 7b153c36e10f1d4e2be2a0cf42f998c31cb6473a
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "58896585"
---
# <a name="migrate-existing-azure-service-bus-standard-namespaces-to-premium-tier"></a>Migrar existente do Azure Service Bus espaços de nomes padrão para o escalão Premium

Anteriormente, o Azure Service Bus oferecido espaços de nomes apenas no escalão Standard. Elas eram configurações de multi-inquilinos foram otimizados para baixo débito e de ambientes de programador.

No passado recente, o Azure Service Bus foi expandido para oferecer o escalão Premium, que oferece recursos dedicados por espaço de nomes para latência previsível e maior débito por um preço fixo que está otimizado para débito elevado e ambientes de produção exigir que funcionalidades empresariais adicionais.

A seguir ativa existentes de espaços de nomes do escalão Standard para ser migrada para o escalão Premium de ferramentas.

>[!WARNING]
> Migração destina-se para o espaço de nomes do Service Bus Standard seja ***atualizado*** para o escalão Premium.
>
> As ferramentas de migração ***não*** suporta a desatualização.
>[!NOTE]
> Esta migração destina-se a acontecer ***in-loco***.
>
> Isso implica que remetente existente e aplicações de destinatário não necessitam de qualquer alteração de código ou configuração.
>
> A cadeia de ligação existente será automaticamente ponto para o novo espaço de nomes premium.
>
> Além disso, todas as entidades no espaço de nomes Standard têm **copiadas** no espaço de nomes Premium durante o processo de migração.
>
>
> Suportamos ***entidades de 1000 por unidade de mensagens*** no Premium, portanto, para identificar o número de unidades de mensagens, precisa, proceda à inicialização com o número de entidades que tiver no seu espaço de nomes padrão atual.

## <a name="migration-steps"></a>Passos de migração

>[!IMPORTANT]
> Existem algumas limitações associadas ao processo de migração. Pedimos-o a familiarizar-se totalmente com as etapas envolvidas para reduzir as possibilidades de erros.

O processo de migração de passo a passo concretas está detalhado nos guias abaixo.

A lógica de passos envolvidos é-

1. Crie um novo espaço de nomes Premium.
2. Emparelhe o espaço de nomes Standard e Premium entre si.
3. Entidades de sincronização (a cópia de ativação pós-falha) do plano padrão para o espaço de nomes Premium
4. Consolidar a migração
5. Drenar entidades no espaço de nomes padrão usando o nome de pós-migração do espaço de nomes
6. Eliminar o espaço de nomes Standard

>[!NOTE]
> Depois da migração foi confirmada, é extremamente importante acessar o espaço de nomes padrão antigo e drenar as filas e subscrições.
>
> Assim que as mensagens têm sido drenadas eles podem ser enviados para o novo espaço de nomes premium a ser processado pelos aplicativos do recetor.
>
> Depois de tem sido drenadas a filas e subscrições, recomendamos a eliminar o espaço de nomes padrão antigo. Não ser que precisam-lo!

### <a name="migrate-using-azure-cli-or-powershell"></a>Migrar com a CLI do Azure ou o PowerShell

Para migrar o espaço de nomes do Service Bus Standard para Premium com a ferramenta Azure CLI ou o PowerShell, consulte a seguir guia.

1. Crie um novo espaço de nomes Premium do Service Bus. Pode fazer referência a [modelos Azure Resource Manager](service-bus-resource-manager-namespace.md) ou [utilizar o portal do Azure](service-bus-create-namespace-portal.md). Certifique-se de que seleciona "Premium" para o **serviceBusSku** parâmetro.

2. Definir o abaixo as variáveis de ambiente para simplificar os comandos de migração.
   ```
   resourceGroup = <resource group for the standard namespace>
   standardNamespace = <standard namespace to migrate>
   premiumNamespaceArmId = <Azure Resource Manager ID of the Premium namespace to migrate to>
   postMigrationDnsName = <post migration DNS name entry to access the Standard namespace>
   ```

    >[!IMPORTANT]
    > O nome de pós-migração (post_migration_dns_name) será utilizado para aceder a migração de postagem antigo do espaço de nomes Standard. Tem de utilizar isto para drenar as filas e das subscrições e, em seguida, elimine o espaço de nomes.

3. **Par** os espaços de nomes Standard e Premium e **Iniciar sincronização** usando o comando - abaixo

    ```
    az servicebus migration start --resource-group $resourceGroup --name $standardNamespace --target-namespace $premiumNamespaceArmId --post-migration-name $postMigrationDnsName
    ```


4. Verificar o estado da migração usando o comando - abaixo
    ```
    az servicebus migration show --resource-group $resourceGroup --name $standardNamespace
    ```

    A migração será considerada concluída quando
    * MigrationState = "Ativo"
    * pendingReplicationsOperationsCount = 0
    * provisioningState = "Com êxito"

    Este comando também apresenta a configuração de migração. . Verifique novamente para garantir que os valores estão definidos como anteriores declarado.

    Além disso, também verificar o espaço de nomes Premium no portal para garantir que todas as filas e tópicos foram criados e que correspondem o que existia no espaço de nomes Standard.

5. Consolidar a migração ao executar o comando completo abaixo
   ```
   az servicebus migration complete --resource-group $resourceGroup --name $standardNamespace
   ```

### <a name="migrate-using-azure-portal"></a>Migrar com o portal do Azure

Migração através do portal do Azure tem o mesmo fluxo lógico como migrar com os comandos. Consulte a seguir guia para o processo passo a passo para migrar com o portal.

1. Escolha o **"Para migrar para o Premium"** opção de menu no menu de navegação no painel da esquerda. Clique nas **'Get Started'** botão para continuar para a página seguinte.
    ![Página de destino de migração][]

2. Concluída **configuração**.
   ![Espaço de nomes de configuração][]
   1. Crie e atribua o espaço de nomes Premium para migrar o espaço de nomes padrão existente para.
        ![Configurar o espaço de nomes - criar o espaço de nomes premium][]
   2. Escolha o **"Name após migração"** para acessar o namespace padrão por após a migração estar concluída.
        ![Configurar o espaço de nomes - escolher o nome de migração de postagem][]
   3. Clique em **'Seguinte'** para continuar.
3. **Sincronização** entidades entre o espaço de nomes Standard e Premium.
    ![Configurar o espaço de nomes - entidades de sincronização - início][]

   1. Clique em **'Sync Iniciar'** para iniciar a sincronização de entidades.
   2. Clique em **'Sim'** no pop-up confirmando para iniciar a sincronização.
   3. Aguarde até que o **sincronização** estiver concluída. O estado é disponibilizado na barra de status.
        ![Espaço de nomes - entidades de sincronização - progresso da configuração][]
        >[!IMPORTANT]
        > Se precisar **abortar** por qualquer motivo, reveja o fluxo de abortar na secção de FAQ deste documento.
   4. Quando a sincronização estiver concluída, clique nas **'Seguinte'** na parte inferior da página.

4. Reveja as alterações na página de resumo.
    ![Espaço de nomes do comutador - menu de comutador][]

5. Clique em **"Concluir a migração"** alternar espaços de nomes e concluir a migração.
    ![Espaço de nomes do comutador - êxito][]

## <a name="faqs"></a>FAQs

### <a name="what-happens-when-the-migration-is-committed"></a>O que acontece quando a migração está empenhada?

Após a migração está comprometida, a cadeia de ligação que apontava para o espaço de nomes Standard irá apontar para o espaço de nomes Premium.

Os aplicativos de remetente e o receptor serão desligar do espaço de nomes padrão e voltar a ligar ao espaço de nomes Premium automaticamente.

### <a name="what-do-i-do-after-the-standard-to-premium-migration-is-complete"></a>O que fazer posso fazer depois de concluída a norma para a migração do Premium?

O padrão para a migração Premium garante que os metadados de entidade (tópicos, subscrições, filtros, et al.) é copiado através do padrão para o espaço de nomes Premium. Os dados da mensagem que foi consolidados para o espaço de nomes padrão não são copiados do padrão para o espaço de nomes Premium.

Por isso, o espaço de nomes Standard pode ter algumas mensagens que foram enviadas e consolidadas enquanto a migração estava em curso. Essas mensagens tem de ser manualmente drenadas do espaço de nomes padrão e enviadas através de espaço de nomes Premium manualmente.

Para fazer isso, ***tem*** utilizar uma aplicação de consola ou script que drenar as entidades de espaço de nomes Standard utilizando o **nome DNS de migração de postagem** que especificou nos comandos de migração e, em seguida, envie-os de mensagens em o espaço de nomes Premium, para que eles podem ser processados pelos recetores.

Depois de tem sido drenadas as mensagens, prossiga para eliminar o espaço de nomes Standard.

>[!IMPORTANT]
> Tenha em atenção que depois de tem sido drenadas as mensagens do espaço de nomes padrão, **tem** eliminar o espaço de nomes Standard.
>
> Isso é importante porque a cadeia de ligação que inicialmente chamado de espaço de nomes Standard agora, na verdade, se refere ao espaço de nomes Premium. Não ser que precisam deste espaço de nomes padrão mais.
>
> A eliminar o espaço de nomes padrão que migrou ajuda reduz a confusão numa data posterior. 

### <a name="how-much-downtime-do-i-expect"></a>Quanto tempo de inatividade posso esperar?
O processo de migração descrito acima destina-se para reduzir o tempo de inatividade esperado para as aplicações. Isso é feito utilizando a cadeia de ligação que os aplicativos de remetente e o receptor utilizam para apontar para o novo espaço de nomes Premium.

O tempo de inatividade experientes pelo aplicativo está limitado à quantidade de tempo que demora a atualizar a entrada DNS para apontar para o espaço de nomes Premium.

Isso pode ser considerado como estando ***aproximadamente 5 minutos***.

### <a name="do-i-have-to-make-any-configuration-changes-while-performing-the-migration"></a>É necessário efetuar quaisquer alterações de configuração ao efetuar a migração?
Não, não há nenhuma alteração de código/configuração necessárias para efetuar a migração. A cadeia de ligação que os aplicativos de remetente e o receptor usar para acessar o Namespace padrão é automaticamente mapeada para atuar como um **alias** para o espaço de nomes Premium.

### <a name="what-happens-when-i-abort-the-migration"></a>O que acontece quando eu abortar a migração?
Migração pode ser anulada, utilizando o comando "Abortar" ou através do portal do Azure. 

#### <a name="azure-cli-or-powershell"></a>CLI do Azure ou o PowerShell

    az servicebus migration abort --resource-group $resourceGroup --name $standardNamespace

#### <a name="azure-portal"></a>Portal do Azure

![Abortar fluxo - sincronização de abortar][]
![abortar fluxo - abortar concluída][]

Quando o processo de migração é abortado, ele realmente interrompe o processo de copiar as entidades (tópicos, subscrições e filtros) do plano padrão para o espaço de nomes Premium e interrompe o emparelhamento.

A cadeia de ligação **není** atualizado para apontar para o espaço de nomes Premium. Os aplicativos existentes continuam a funcionar como antes de iniciar a migração.

No entanto, ele **não** eliminar as entidades no espaço de nomes Premium ou eliminar o espaço de nomes Premium em si. Isso precisa ser feito manualmente se tinha decidiu não seguir em frente com a migração Afinal de contas.

>[!IMPORTANT]
> Se optar por abortar a migração, elimine o espaço de nomes Premium que tinha aprovisionado para a migração, para que não lhe é cobrados os recursos.

#### <a name="i-dont-want-to-have-to-drain-the-messages-what-do-i-do"></a>Não quero ter drenar as mensagens. O que posso fazer?

Pode haver mensagens enviadas pelos aplicativos remetente e dedicadas para o armazenamento no espaço de nomes Standard, enquanto a migração está a ocorrer e, logo antes da migração está comprometido.

Uma vez que durante a migração, o mensagem real/payload de dados não é copiado através de Standard para Premium, estes têm de ser drenados manualmente e, em seguida, enviado para o espaço de nomes Premium.

No entanto, se pode migrar durante uma janela de manutenção planeada/manutenção e não quer manualmente drenar e enviar as mensagens,. Siga os passos - abaixo

1. Pare os aplicativos de remetente e permitir que os recetores processar as mensagens que estão atualmente no espaço de nomes padrão e drenagem a fila.
2. Assim que estiverem vazias, a filas e subscrições no espaço de nomes Standard, siga o procedimento descrito acima para executar a migração do plano padrão para o espaço de nomes Premium.
3. Quando a migração estiver concluída, pode reiniciar os aplicativos do remetente.
4. Os remetentes e recetores agora irão ligar automaticamente com o espaço de nomes Premium.

    >[!NOTE]
    > O destinatário não tem de ser parado para a migração.
    >
    > Quando a migração estiver concluída, os recetores irão desligar do espaço de nomes padrão e ligar automaticamente ao espaço de nomes Premium.

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre o [diferenças entre o Standard e Premium de mensagens](./service-bus-premium-messaging.md)
* Saiba mais sobre os aspectos de recuperação de elevada disponibilidade e Geo Diaster para Premium do Service Bus [aqui](service-bus-outages-disasters.md#protecting-against-outages-and-disasters---service-bus-premium)

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
