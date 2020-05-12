---
title: Pontos Finais Privados
description: Compreenda o processo de criação de pontos finais privados para o Azure Backup e os cenários em que a utilização de pontos finais privados ajuda a manter a segurança dos seus recursos.
ms.topic: conceptual
ms.date: 05/07/2020
ms.openlocfilehash: bc778506819c44291bb2d8f69cdd9ac0aed51399
ms.sourcegitcommit: 801a551e047e933e5e844ea4e735d044d170d99a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/11/2020
ms.locfileid: "83007859"
---
# <a name="private-endpoints-for-azure-backup"></a>Pontos finais privados para backup azure

O Azure Backup permite-lhe fazer backup e restaurar os seus dados dos cofres dos serviços de recuperação utilizando [pontos finais privados](https://docs.microsoft.com/azure/private-link/private-endpoint-overview). Os pontos finais privados utilizam um ou mais endereços IP privados do seu VNet, efetivamente trazendo o serviço para o seu VNet.

Este artigo irá ajudá-lo a entender o processo de criação de pontos finais privados para o Azure Backup e os cenários em que a utilização de pontos finais privados ajuda a manter a segurança dos seus recursos.

## <a name="before-you-start"></a>Antes de começar

- Os pontos finais privados só podem ser criados para novos cofres de Serviços de Recuperação (que não têm quaisquer itens registados no cofre). Por isso, devem ser criados pontos finais privados antes de tentar proteger quaisquer itens para o cofre.
- Uma rede virtual pode conter pontos finais privados para vários cofres dos Serviços de Recuperação. Além disso, um cofre de Serviços de Recuperação pode ter pontos finais privados para ele em várias redes virtuais. No entanto, o número máximo de pontos finais privados que podem ser criados para um cofre é de 12.
- Assim que um ponto final privado for criado para um cofre, o cofre será fechado. Não será acessível (para backups e restauros) de redes para além das que contêm um ponto final privado para o cofre. Se todos os pontos finais privados para o cofre forem removidos, o cofre estará acessível a partir de todas as redes.
- Enquanto um cofre de Serviços de Recuperação é usado por (ambos) Backup Azure e Recuperação de Site Azure, este artigo discute o uso de pontos finais privados apenas para Backup Azure.
- O Azure Ative Directory não suporta atualmente pontos finais privados. Assim, os IPs e fQDNs necessários para que o Azure Ative Directory funcione numa região terão de ser autorizados a aceder à saída da rede segura quando realizarem cópias de segurança em VMs Azure e cópia de segurança utilizando o agente MARS. Também pode utilizar tags NSG e tags Azure Firewall para permitir o acesso ao Azure AD, conforme aplicável.
- As redes virtuais com Políticas de Rede não são suportadas para pontos finais privados. Tens de desativar a Polícia da Rede antes de continuares.

## <a name="recommended-and-supported-scenarios"></a>Cenários recomendados e apoiados

Enquanto os pontos finais privados estão ativados para o cofre, são usados para backup e restauro de cargas de trabalho SQL e SAP HANA apenas em um cofre De VM Azure e MARS. Você pode usar o cofre para backup de outras cargas de trabalho também (eles não exigiriam pontos finais privados no entanto). Além da cópia de segurança das cargas de trabalho SQL e SAP HANA e de backup utilizando o agente MARS, os pontos finais privados também são usados para realizar a recuperação de ficheiros no caso de backup Azure VM. Para mais informações, consulte a seguinte tabela:

| Backup de cargas de trabalho em Azure VM (SQL, SAP HANA), Backup usando mars agent | Recomenda-se a utilização de pontos finais privados para permitir a cópia de segurança e restauro sem necessidade de permitir a lista de IPs/FQDNs para backup azure ou armazenamento azure das suas redes virtuais. |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| **Backup Azure VM**                                         | A cópia de segurança VM não requer que permita o acesso a quaisquer IPs ou FQDNs. Portanto, não requer pontos finais privados para cópia de segurança e restauro de discos.  <br><br>   No entanto, a recuperação de ficheiros de um cofre contendo pontos finais privados seria restrita a redes virtuais que contenham um ponto final privado para o cofre. <br><br>    Ao utilizar discos não geridos da ACL'ed, certifique-se de que a conta de armazenamento que contém os discos permite o acesso a **serviços confiáveis** da Microsoft se for ACL'ed. |
| **Backup de Ficheiros Azure**                                      | As cópias de segurança do Azure Files são armazenadas na conta de armazenamento local. Portanto, não requer pontos finais privados para reforços e restauros. |

## <a name="creating-and-using-private-endpoints-for-backup"></a>Criação e utilização de pontos finais privados para backup

Esta secção fala sobre os passos envolvidos na criação e utilização de pontos finais privados para o Azure Backup dentro das suas redes virtuais.

>[!IMPORTANT]
> É altamente recomendável que siga os passos na mesma sequência que mencionado neste documento. Se não o fizer, o cofre pode ser tornado incompatível com a utilização de pontos finais privados e exigir que reinicie o processo com um novo cofre.

>[!NOTE]
> Alguns elementos da experiência do portal Azure podem não estar disponíveis atualmente. Consulte as experiências alternativas nestes cenários até à sua disponibilidade total na sua região.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

Consulte [esta secção](#create-a-recovery-services-vault-using-the-azure-resource-manager-client) para aprender a criar um cofre utilizando o cliente do Gestor de Recursos Azure. Isto cria um cofre com a sua identidade gerida já ativada. Saiba mais sobre os cofres dos Serviços de Recuperação [aqui.](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview)

## <a name="enable-managed-identity-for-your-vault"></a>Ativar identidade gerida para o seu cofre

Identidades geridas permitem que o cofre crie e use pontos finais privados. Esta secção fala em permitir a identidade gerida para o seu cofre.

1. Vá ao cofre dos Serviços de Recuperação - **identidade**>.

    ![Alterar o estatuto de identidade para On](./media/private-endpoints/identity-status-on.png)

1. Altere o **Estado** para **On** e clique em **Guardar**.

1. É gerada uma identificação de **objeto,** que é a identidade gerida do cofre.

    >[!NOTE]
    >Uma vez ativada, a Identidade Gerida NÃO deve ser desativada (mesmo temporariamente). Desativar a identidade gerida pode levar a um comportamento inconsistente.

## <a name="dns-changes"></a>Alterações dNS

A utilização de pontos finais privados requer zonas Privadas de DNS para permitir que a extensão de backup resolva o link privado FQDNs para iPs privados. Ao todo, são necessárias três zonas privadas de DNS. Embora duas destas zonas devam ser criadas obrigatoriamente, a terceira pode ser optada por ser integrada com o ponto final privado (enquanto cria o ponto final privado) ou pode ser criada separadamente.

Também pode utilizar os seus servidores DNS personalizados. Consulte [as alterações dNS para servidores DNS personalizados](#dns-changes-for-custom-dns-servers) para mais detalhes sobre a utilização de servidores DNS personalizados.

### <a name="creating-mandatory-dns-zones"></a>Criação de zonas DNS obrigatórias

Existem duas zonas dNS obrigatórias que precisam de ser criadas:

- `privatelink.blob.core.windows.net`(para dados de cópia de segurança/restauro)
- `privatelink.queue.core.windows.net`(para comunicação de serviço)

1. Procure a **Private DNS Zone** na barra de pesquisa de **todos os serviços** e selecione **a zona Privada DNS** da lista de drop-down.

    ![Selecione zona Privada DNS](./media/private-endpoints/private-dns-zone.png)

1. Uma vez no painel **de zona DNS privado,** clique no botão **+Adicionar** para começar a criar uma nova zona.

1. No painel de **zona DNS privado Create,** preencha os detalhes necessários. A subscrição deve ser a mesma que será criada o ponto final privado.

    As zonas devem ser denominadas como:

    - `privatelink.blob.core.windows.net`
    - `privatelink.queue.core.windows.net`

    | **Zona**                           | **Serviço** | **Detalhes do Grupo de Subscrição e Recursos (RG)**                  |
    | ---------------------------------- | ----------- | ------------------------------------------------------------ |
    | `privatelink.blob.core.windows.net`  | Blobs        | **Subscrição**: O mesmo local onde o ponto final privado precisa de ser criado **RG**: Ou o RG do VNET ou o do Ponto final privado |
    | `privatelink.queue.core.windows.net` | Filas       | **RG**: Ou o RG do VNET ou o do Ponto Final Privado |

    ![Criar zona Privada dNS](./media/private-endpoints/create-private-dns-zone.png)

1. Uma vez feito, proceda à revisão e crie a zona DNS.

### <a name="optional-dns-zone"></a>Zona opcional de DNS

Os clientes podem optar por integrar os seus pontos finais privados com zonas privadas de DNS para Backup Azure (discutidona secção sobre a criação de pontos finais privados) para comunicação de serviços. Se não quiser integrar-se com a zona Privada DNS, pode optar por utilizar o seu próprio servidor DNS ou criar uma zona privada de DNS separadamente. Isto para além das duas zonas privadas obrigatórias de DNS discutidas na secção anterior.

Se desejar criar uma zona Privada DNS separada em Azure, pode fazer o mesmo utilizando os mesmos passos utilizados para criar zonas DNS obrigatórias. Os detalhes de nomeação e subscrição são partilhados abaixo:

| **Zona**                                                     | **Serviço** | **Detalhes do Grupo de Subscrição e Recursos**                  |
| ------------------------------------------------------------ | ----------- | ------------------------------------------------------------ |
| `privatelink.<geo>.backup.windowsazure.com`  <br><br>   **Nota:** *geo* aqui refere-se ao código da região. Por exemplo, *wcus* e *ne* para os EUA centrais ocidentais e norte da Europa, respectivamente. | Cópia de segurança      | **Subscrição**: O mesmo local onde o Ponto Final Privado precisa de ser criado **RG**: Qualquer RG dentro da subscrição |

Consulte [esta lista](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/AzureRegionCodesList.docx) para códigos de região.

Para convenções de nomeação de URL em geos nacionais:

- [China](https://docs.microsoft.com/azure/china/resources-developer-guide#check-endpoints-in-azure)
- [Alemanha](https://docs.microsoft.com/azure/germany/germany-developer-guide#endpoint-mapping)
- [US Gov](https://docs.microsoft.com/azure/azure-government/documentation-government-developer-guide)

### <a name="linking-private-dns-zones-with-your-virtual-network"></a>Ligando zonas privadas de DNS com a sua rede virtual

As zonas DNS criadas acima devem agora estar ligadas à rede virtual onde os seus servidores a serem apoiados estão localizados. Isto tem de ser feito para todas as zonas de DNS que criaste.

1. Vá à sua zona DNS (que criou no passo anterior) e navegue para **ligações** de rede Virtual na barra esquerda. Uma vez lá, clique no botão **+Adicionar**
1. Preencha os detalhes necessários. Os campos de **rede Subscrição** e **Virtual** devem ser preenchidos com detalhes correspondentes da rede virtual onde os seus servidores existem. Os outros campos devem ser deixados como está.

    ![Adicionar ligação de rede virtual](./media/private-endpoints/add-virtual-network-link.png)

## <a name="grant-permissions-to-the-vault-to-create-required-private-endpoints"></a>Conceda permissões ao cofre para criar pontos finais privados necessários

Para criar os pontos finais privados necessários para o Azure Backup, o cofre (a Identidade Gerida do cofre) deve ter permissões para os seguintes grupos de recursos:

- O Grupo de Recursos que contém o alvo VNet
- O Grupo de Recursos onde os Pontos Finais Privados devem ser criados
- O Grupo de Recursos que contém as zonas privadas de DNS

Recomendamos que conceda o papel de **Contribuinte** para esses três grupos de recursos ao cofre (identidade gerida). As seguintes etapas descrevem como fazê-lo para um determinado grupo de recursos (isto tem de ser feito para cada um dos três grupos de recursos):

1. Vá ao Grupo de Recursos e navegue para o Controlo de **Acesso (IAM)** na barra esquerda.
1. Uma vez no Controlo de **Acesso,** vá adicionar uma atribuição de **funções.**

    ![Adicionar uma atribuição de função](./media/private-endpoints/add-role-assignment.png)

1. No painel de atribuição de **funções Add,** escolha **O Colaborador** como **Papel**, e use o **nome** do cofre como **principal**. Selecione o seu cofre e clique em **Guardar** quando estiver feito.

    ![Escolha o papel e o diretor](./media/private-endpoints/choose-role-and-principal.png)

Para gerir permissões a um nível mais granular, consulte [Criar funções e permissões manualmente](#create-roles-and-permissions-manually).

## <a name="creating-and-approving-private-endpoints-for-azure-backup"></a>Criação e aprovação de pontos finais privados para backup do Azure

### <a name="creating-private-endpoints-for-backup"></a>Criação de pontos finais privados para backup

Esta secção descreve o processo de criação de um ponto final privado para o seu cofre.

1. Na barra de pesquisa, procure e selecione **Private Link**. Isto leva-o ao **Private Link Center.**

    ![Pesquisa por Private Link](./media/private-endpoints/search-for-private-link.png)

1. Na barra de navegação à esquerda, clique em **Pontos Finais Privados**. Uma vez no painel **Private Endpoints,** clique em **+Adicionar** para começar a criar um Ponto Final Privado para o seu cofre.

    ![Adicione ponto final privado no Private Link Center](./media/private-endpoints/add-private-endpoint.png)

1. Uma vez no processo **Create Private Endpoint,** será necessário especificar detalhes para criar a sua ligação de ponto final privado.

    1. **Básicos**: Preencha os detalhes básicos para os seus pontos finais privados. A região deve ser a mesma que o cofre e o recurso.

        ![Preencha detalhes básicos](./media/private-endpoints/basic-details.png)

    1. **Recurso**: Este separador requer que mencione o recurso PaaS para o qual pretende criar a sua ligação. Selecione **Microsoft.RecoveryServices/vaults** do tipo de recurso para a subscrição desejada. Uma vez feito, escolha o nome do seu cofre de Serviços de Recuperação como **Recurso** e **AzureBackup** como **subrecurso Target**.

        ![Preencha o separador De recursos](./media/private-endpoints/resource-tab.png)

    1. **Configuração**: Na configuração, especifique a rede virtual e a sub-rede onde pretende que o ponto final privado seja criado. Este seria o Vnet onde o VM está presente. Pode optar por **integrar o seu ponto final privado** com uma zona privada de DNS. Em alternativa, também pode utilizar o seu servidor DNS personalizado ou criar uma zona Privada de DNS.

        ![Preencha o separador de configuração](./media/private-endpoints/configuration-tab.png)

    1. Opcionalmente, pode adicionar **Tags** para o seu ponto final privado.

    1. Proceda ao **Review + crie** uma vez feito os detalhes de introdução. Quando a validação estiver concluída, clique em **Criar** o ponto final privado.

## <a name="approving-private-endpoints"></a>Aprovação de pontos finais privados

Se o utilizador que cria o ponto final privado for também o proprietário do cofre dos Serviços de Recuperação, o ponto final privado acima criado será aprovado automaticamente. Caso contrário, o proprietário do cofre deve aprovar o ponto final privado antes de poder usá-lo. Esta secção discute a aprovação manual de pontos finais privados através do portal Azure.

Consulte a [aprovação manual de pontos finais privados utilizando o Cliente do Gestor](#manual-approval-of-private-endpoints-using-the-azure-resource-manager-client) de Recursos Azure para utilizar o cliente do Gestor de Recursos Azure para a aprovação de pontos finais privados.

1. No seu cofre de Serviços de Recuperação, navegue para **ligações de ponto final privado** na barra esquerda.
1. Selecione a ligação de ponto final privado que deseja aprovar.
1. Selecione **Aprovar** na barra superior. Também pode selecionar **Rejeitar** ou **Remover** se pretender rejeitar ou eliminar a ligação de ponto final.

    ![Aprovar pontos finais privados](./media/private-endpoints/approve-private-endpoints.png)

## <a name="adding-dns-records"></a>Adicionar registos dNS

>[!NOTE]
> Este passo não é necessário se estiver a utilizar uma zona DNS integrada. No entanto, se criou a sua própria zona DeNs Privada Azure ou está a utilizar uma zona privada personalizada de DNS, certifique-se de que as entradas são feitas conforme descrito nesta secção.

Depois de ter criado a zona privada opcional de DNS e os pontos finais privados para o seu cofre, terá de adicionar registos DNS à sua zona de DNS. Pode fazê-lo manualmente ou usando um script PowerShell. Isto tem de ser feito apenas para a sua zona DNS de reserva, as para Blobs e Queues serão automaticamente atualizadas.

### <a name="add-records-manually"></a>Adicione registos manualmente

Isto requer que efetue entradas para cada FQDN no seu ponto final privado na sua Zona DNS Privada.

1. Vá à sua **zona privada de DNS** e navegue até à opção **Overview** na barra esquerda. Uma vez lá, clique em **+Record definido** para começar a adicionar registos.

    ![Selecione +Record set para adicionar registos](./media/private-endpoints/select-record-set.png)

1. No painel **Add Record set** que se abre, adicione uma entrada para cada FQDN e IP privado como um registo de tipo **A.** A lista de FQDNs e IPs pode ser obtida a partir do seu Ponto Final Privado (sob **visão geral).** Como mostra o exemplo abaixo, o primeiro FQDN do ponto final privado está a ser adicionado ao recorde estabelecido na zona privada de DNS.

    ![Lista de FQDNs e IPs](./media/private-endpoints/list-of-fqdn-and-ip.png)

    ![Adicionar conjunto de registos](./media/private-endpoints/add-record-set.png)

### <a name="add-records-using-powershell-script"></a>Adicione registos usando o script PowerShell

1. Inicie a **Cloud Shell** no portal Azure e selecione o **ficheiro Upload** na janela PowerShell.

    ![Selecione ficheiro upload na janela PowerShell](./media/private-endpoints/upload-file-in-powershell.png)

1. Faça upload deste script: [DnsZoneCreation](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/dnszonerecordcreation.ps1)

1. Vá para a sua pasta inicial (por exemplo: `cd /home/user` )

1. Execute o seguintes script:

    ```azurepowershell
    ./dnszonerecordcreation.ps1 -Subscription <SubscriptionId> -VaultPEName <VaultPE Name> -VaultPEResourceGroup <Vault PE RG> -DNSResourceGroup <Private DNS RG> -Privatezone <privatednszone>
    ```

    Estes são os parâmetros:

    - **subscrição**: A subscrição em que residem os recursos (ponto final privado do cofre e zona privada de DNS)
    - **vaultPEName**: Nome do ponto final privado criado para o cofre
    - **vaultPEResourceGroup**: Grupo de recursos que contém o ponto final privado do cofre
    - **dnsResourceGroup**: Grupo de recursos que contém as zonas privadas de DNS
    - **Privatezone**: Nome da zona privada de DNS

## <a name="using-private-endpoints-for-backup"></a>Utilização de pontos finais privados para backup

Uma vez aprovados os pontos finais privados criados para o cofre do seu VNet, pode começar a usá-los para executar as suas cópias de segurança e restauros.

>[!IMPORTANT]
>Certifique-se de que completou todos os passos acima mencionados no documento com sucesso antes de prosseguir. Para recapitular, deve ter completado os passos na seguinte lista de verificação:
>
>1. Criou um (novo) Cofre de Serviços de Recuperação
>1. Permitiu que o cofre usasse o sistema atribuído identidade gerida
>1. Criou três zonas Privadas de DNS (duas se utilizar uma zona DNS integrada para backup)
>1. Ligou as suas zonas Privadas de DNS à sua Rede Virtual Azure
>1. Atribuídas permissões relevantes para a Identidade Gerida do cofre
>1. Criei um ponto final privado para o seu cofre
>1. Aprovado o Ponto Final Privado (se não for aprovado automaticamente)
>1. Adicionado sintetização dNS necessárias à sua zona privada de DNS para Backup (aplicável apenas se não utilizar uma zona privada integrada de DNS)

### <a name="backup-and-restore-of-workloads-in-azure-vm-sql-sap-hana"></a>Backup e restauro de cargas de trabalho em Azure VM (SQL, SAP HANA)

Uma vez que o ponto final privado é criado e aprovado, não são necessárias alterações adicionais do lado do cliente para usar o ponto final privado. Toda a comunicação e transferência de dados da sua rede segura para o cofre será realizada através do ponto final privado.
No entanto, se remover pontos finais privados para o cofre depois de um servidor (SQL/SAP HANA) ter sido registado no mesmo, terá de voltar a registar o recipiente com o cofre. Não precisas de parar a proteção deles.

### <a name="backup-and-restore-through-mars-agent"></a>Backup e restaurar através do Agente MARS

Ao utilizar o Agente MARS para fazer o apoio aos seus recursos no local, certifique-se de que a sua rede no local (contendo os seus recursos a serem apoiados) é espreitada com o Azure VNet que contém um ponto final privado para o cofre, para que possa usá-lo. Pode então continuar a instalar o agente MARS e configurar a cópia de segurança conforme detalhado aqui. No entanto, deve garantir que todas as comunicações para backup ocorram apenas através da rede peered.

No entanto, se remover pontos finais privados para o cofre depois de um agente mars ter sido registado nele, terá de voltar a registar o recipiente com o cofre. Não precisas de parar a proteção deles.

## <a name="additional-topics"></a>Tópicos adicionais

### <a name="create-a-recovery-services-vault-using-the-azure-resource-manager-client"></a>Crie um cofre de Serviços de Recuperação utilizando o cliente do Gestor de Recursos Azure

Pode criar o Cofre de Serviços de Recuperação e ativar a sua Identidade Gerida (é necessária a identidade gerida, como veremos mais tarde) utilizando o cliente do Gestor de Recursos Azure. Uma amostra para fazer isto é partilhada abaixo:

```rest
armclient PUT /subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>?api-version=2017-07-01-preview @C:\<filepath>\MSIVault.json
```

O ficheiro JSON acima deve ter o seguinte conteúdo:

Pedido JSON:

```json
{
  "location": "eastus2",
  "name": "<vaultname>",
  "etag": "W/\"datetime'2019-05-24T12%3A54%3A42.1757237Z'\"",
  "tags": {
    "PutKey": "PutValue"
  },
  "properties": {},
  "id": "/subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>",
  "type": "Microsoft.RecoveryServices/Vaults",
  "sku": {
    "name": "RS0",
    "tier": "Standard"
  },
  "identity": {
    "type": "systemassigned"
  }
}
```

Resposta JSON:

```json
{
   "location": "eastus2",
   "name": "<vaultname>",
   "etag": "W/\"datetime'2020-02-25T05%3A26%3A58.5181122Z'\"",
   "tags": {
     "PutKey": "PutValue"
   },
   "identity": {
     "tenantId": "<tenantid>",
     "principalId": "<principalid>",
     "type": "SystemAssigned"
   },
   "properties": {
     "provisioningState": "Succeeded",
     "privateEndpointStateForBackup": "None",
     "privateEndpointStateForSiteRecovery": "None"
   },
   "id": "/subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>",
   "type": "Microsoft.RecoveryServices/Vaults",
   "sku": {
     "name": "RS0",
     "tier": "Standard"
   }
 }
```

>[!NOTE]
>O cofre criado neste exemplo através do cliente do Gestor de Recursos Azure já é criado com uma identidade gerida atribuída pelo sistema.

### <a name="managing-permissions-on-resource-groups"></a>Gestão de permissões em Grupos de Recursos

A Identidade Gerida para o cofre precisa de ter as seguintes permissões no grupo de recursos e na rede virtual onde serão criados os pontos finais privados:

- `Microsoft.Network/privateEndpoints/*`Isto é necessário para executar CRUD em pontos finais privados no grupo de recursos. Deve ser atribuído no grupo de recursos.
- `Microsoft.Network/virtualNetworks/subnets/join/action`Isto é exigido na rede virtual onde o IP privado está a ser ligado ao ponto final privado.
- `Microsoft.Network/networkInterfaces/read`Isto é necessário no grupo de recursos para obter a interface de rede criada para o ponto final privado.
- Papel de colaborador privado da Zona DNS Esta função já existe e pode ser usada para fornecer `Microsoft.Network/privateDnsZones/A/*` e `Microsoft.Network/privateDnsZones/virtualNetworkLinks/read` permissões.

Pode utilizar um dos seguintes métodos para criar funções com permissões necessárias:

#### <a name="create-roles-and-permissions-manually"></a>Criar funções e permissões manualmente

Crie os seguintes ficheiros JSON e utilize o comando PowerShell no final da secção para criar funções:

PrivateEndpointContributorRoleDef.json

```json
{
  "Name": "PrivateEndpointContributor",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows management of Private Endpoint",
  "Actions": [
    "Microsoft.Network/privateEndpoints/*",
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000"
  ]
}
```

NetworkInterfaceReaderRoleDef.json

```json
{
  "Name": "NetworkInterfaceReader",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows read on networkInterfaces",
  "Actions": [
    "Microsoft.Network/networkInterfaces/read"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000"
  ]
}
```

PrivateEndpointSubnetContributorRoleDef.json

```json
{
  "Name": "PrivateEndpointSubnetContributor",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows adding of Private Endpoint connection to Virtual Networks",
  "Actions": [
    "Microsoft.Network/virtualNetworks/subnets/join/action"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000"
  ]
}
```

```azurepowershell
 New-AzRoleDefinition -InputFile "PrivateEndpointContributorRoleDef.json"
 New-AzRoleDefinition -InputFile "NetworkInterfaceReaderRoleDef.json"
 New-AzRoleDefinition -InputFile "PrivateEndpointSubnetContributorRoleDef.json"
```

#### <a name="use-a-script"></a>Use um script

1. Inicie a **Cloud Shell** no portal Azure e selecione o **ficheiro Upload** na janela PowerShell.

    ![Selecione ficheiro upload na janela PowerShell](./media/private-endpoints/upload-file-in-powershell.png)

1. Faça upload do seguinte script: [VaultMsiPrereqScript](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/VaultMsiPrereqScript.ps1)

1. Vá para a sua pasta inicial (por exemplo: `cd /home/user` )

1. Execute o seguintes script:

    ```azurepowershell
    ./VaultMsiPrereqScript.ps1 -subscription <subscription-Id> -vaultPEResourceGroup <vaultPERG> -vaultPESubnetResourceGroup <subnetRG> -vaultMsiName <msiName>
    ```

    Estes são os parâmetros:

    - **subscrição**: **SubscriçãoId que tem o grupo de recursos onde o ponto final privado para o cofre deve ser criado e a subnet onde o ponto final privado do cofre será anexado

    - **vaultPEResourceGroup**: Grupo de recursos onde o ponto final privado para o cofre será criado

    - **vaultPESubnetResourceGroup**: Grupo de recursos da subnet a que o ponto final privado será aderido

    - **vaultMsiName**: Nome do MSI do cofre, que é o mesmo que **O Nome do Cofre**

1. Complete a autenticação e o script terá o contexto da subscrição fornecida acima. Criará as funções apropriadas se faltarem ao inquilino e atribuirão papéis à MSI do cofre.

### <a name="creating-private-endpoints-using-azure-powershell"></a>Criação de pontos finais privados usando o Azure PowerShell

#### <a name="auto-approved-private-endpoints"></a>Pontos finais privados aprovados automaticamente

```azurepowershell
$vault = Get-AzRecoveryServicesVault `
        -ResourceGroupName $vaultResourceGroupName `
        -Name $vaultName
  
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
        -Name $privateEndpointConnectionName `
        -PrivateLinkServiceId $vault.ID `
        -GroupId "AzureBackup"  
  
$privateEndpoint = New-AzPrivateEndpoint `
        -ResourceGroupName $vmResourceGroupName `
        -Name $privateEndpointName `
        -Location $location `
        -Subnet $subnet `
        -PrivateLinkServiceConnection $privateEndpointConnection `
        -Force
```

### <a name="manual-approval-of-private-endpoints-using-the-azure-resource-manager-client"></a>Aprovação manual de pontos finais privados utilizando o Cliente do Gestor de Recursos Do Azure

1. Use **o GetVault** para obter o ID de ligação de endpoint privado para o seu ponto final privado.

    ```rest
    armclient GET /subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/vaults/<vaultname>?api-version=2017-07-01-preview
    ```

    Isto devolverá o ID de ligação de ponto final privado. O nome da ligação pode ser recuperado utilizando a primeira parte do ID de ligação da seguinte forma:

    `privateendpointconnectionid = {peName}.{vaultId}.backup.{guid}`

1. Obtenha o ID de **ligação de ponto final privado** (e o nome de ponto final **privado,** sempre que necessário) da resposta e substitua-o no seguinte JSON e Azure Resource Manager URI e tente mudar o Status para "Aprovado/Rejeitado/Desligado", como demonstrado na amostra abaixo:

    ```rest
    armclient PUT /subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>/privateEndpointConnections/<privateendpointconnectionid>?api-version=2020-02-02-preview @C:\<filepath>\BackupAdminApproval.json
    ```

    JSON:

    ```json
    {
    "id": "/subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>/privateEndpointConnections/<privateendpointconnectionid>",
    "properties": {
        "privateEndpoint": {
        "id": "/subscriptions/<subscriptionid>/resourceGroups/<pergname>/providers/Microsoft.Network/privateEndpoints/pename"
        },
        "privateLinkServiceConnectionState": {
        "status": "Disconnected",  //choose state from Approved/Rejected/Disconnected
        "description": "Disconnected by <userid>"
        }
    }
    }
    ```

### <a name="dns-changes-for-custom-dns-servers"></a>Alterações dNS para servidores DNS personalizados

#### <a name="create-dns-zones-for-custom-dns-servers"></a>Criar zonas DNS para servidores DNS personalizados

Você precisa criar três zonas Privadas de DNS e ligá-las à sua rede virtual.

| **Zona**                                                     | **Serviço** |
| ------------------------------------------------------------ | ----------- |
| `privatelink.<geo>.backup.windowsazure.com`      | Cópia de segurança      |
| `privatelink.blob.core.windows.net`                            | Blobs        |
| `privatelink.queue.core.windows.net`                           | Filas       |

>[!NOTE]
>No texto acima, *geo* refere-se ao código da região. Por exemplo, *wcus* e *ne* para os EUA centrais ocidentais e norte da Europa, respectivamente.

Consulte [esta lista](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/AzureRegionCodesList.docx) para códigos de região.

#### <a name="adding-dns-records-for-custom-dns-servers"></a>Adicionar registos DNS para servidores DNS personalizados

Isto requer que efetue entradas para cada FQDN no seu ponto final privado na sua Zona DNS Privada.

Note-se que usaremos os pontos finais privados criados para o serviço de Backup, Blob e Fila.

- O ponto final privado para o cofre usa o nome especificado enquanto cria o ponto final privado
- Os pontos finais privados para serviços de blob e fila são pré-fixados com o nome do mesmo para o cofre.

Por exemplo, a imagem seguinte mostra os três pontos finais privados criados para uma ligação de ponto final privado com o nome *pee2epe:*

![Três pontos finais privados para uma ligação de ponto final privado](./media/private-endpoints/three-private-endpoints.png)

Zona DNS para o serviço de backup `privatelink.<geo>.backup.windowsazure.com` ():

1. Navegue até ao seu ponto final privado para backup no **Private Link Center**. A página geral lista o FQDN e os IPs privados para o seu ponto final privado.

1. Adicione uma entrada para cada FQDN e IP privado como um registo de tipo A.

    ![Adicionar entrada para cada FQDN e IP privado](./media/private-endpoints/add-entry-for-each-fqdn-and-ip.png)

Zona DNS para o serviço Blob `privatelink.blob.core.windows.net` ():

1. Navegue até ao seu ponto final privado para blob no **Private Link Center**. A página geral lista o FQDN e os IPs privados para o seu ponto final privado.

1. Adicione uma entrada para o FQDN e IP privado como um registo de tipo A.

    ![Adicione a entrada para o FQDN e IP privado como um registo de tipo A para o serviço Blob](./media/private-endpoints/add-type-a-record-for-blob.png)

Zona DNS para o serviço de fila `privatelink.queue.core.windows.net` ():

1. Navegue até ao seu ponto final privado para fila no **Private Link Center**. A página geral lista o FQDN e os IPs privados para o seu ponto final privado.

1. Adicione uma entrada para o FQDN e IP privado como um registo de tipo A.

    ![Adicione a entrada para o FQDN e IP privado como um registo de tipo A para o serviço de fila](./media/private-endpoints/add-type-a-record-for-queue.png)

## <a name="frequently-asked-questions"></a>Perguntas Mais Frequentes

P. Posso criar um ponto final privado para um cofre de reserva existente?<br>
R. Não, os pontos finais privados podem ser criados apenas para novos cofres de reserva. Então o cofre nunca deve ter tido nenhum objeto protegido. Na verdade, nenhuma tentativa de proteger quaisquer itens para o cofre pode ser feita antes de criar pontos finais privados.

P. Tentei proteger um objeto do meu cofre, mas falhou e o cofre ainda não contém objetos protegidos. Posso criar pontos finais privados para este cofre?<br>
R. Não, o cofre não deve ter tentado proteger quaisquer objetos no passado.

P. Tenho um cofre que está a usar pontos finais privados para reforços e restauros. Posso adicionar ou remover pontos finais privados para este cofre, mesmo que eu tenha itens de reserva protegidos?<br>
R. Sim. Se já criou pontos finais privados para um cofre e itens de backup protegidos, pode adicionar ou remover pontos finais privados conforme necessário.

P. O ponto final privado para o Azure Backup também pode ser usado para a Recuperação do Site Azure?<br>
R. Não, o ponto final privado para backup só pode ser usado para backup azure. Você precisará criar um novo ponto final privado para a Recuperação do Site Azure, se for suportado pelo serviço.

P. Perdi um dos passos deste artigo e fui proteger a minha fonte de dados. Ainda posso usar pontos finais privados?<br>
R. Não seguir os passos do artigo e continuar a proteger itens pode levar a que o cofre não possa usar pontos finais privados. Por isso, recomenda-se que se refira a esta lista de verificação antes de proceder à proteção de itens.

P. Posso usar o meu próprio servidor DNS em vez de usar a zona Privada DeNS do Azure ou uma zona dNS privada integrada?<br>
R. Sim, podes usar os teus próprios servidores DNS. No entanto, certifique-se de que todos os registos DNS necessários são adicionados como sugerido nesta secção.

P. Preciso de dar passos adicionais no meu servidor depois de ter seguido o processo neste artigo?<br>
R. Depois de seguir o processo detalhado neste artigo, não precisa de fazer trabalho adicional para utilizar pontos finais privados para backup e restauro.

## <a name="next-steps"></a>Passos seguintes

- Leia sobre todas as [funcionalidades de segurança no Azure Backup](security-overview.md)
