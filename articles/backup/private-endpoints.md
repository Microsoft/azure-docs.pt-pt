---
title: Pontos Finais Privados
description: Compreenda o processo de criação de pontos finais privados para o Azure Backup e os cenários em que a utilização de pontos finais privados ajuda a manter a segurança dos seus recursos.
ms.topic: conceptual
ms.date: 05/07/2020
ms.openlocfilehash: 1775ec2c337dba0a618f9e7d186af9ed11a0e303
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105559388"
---
# <a name="private-endpoints-for-azure-backup"></a>Pontos finais privados para backup Azure

O Azure Backup permite-lhe fazer o backup seguro e restaurar os seus dados a partir dos cofres dos Serviços de Recuperação utilizando [pontos finais privados.](../private-link/private-endpoint-overview.md) Os pontos finais privados utilizam um ou mais endereços IP privados do seu VNet, efetivamente trazendo o serviço para o seu VNet.

Este artigo irá ajudá-lo a entender o processo de criação de pontos finais privados para o Azure Backup e os cenários em que a utilização de pontos finais privados ajuda a manter a segurança dos seus recursos.

## <a name="before-you-start"></a>Antes de começar

- Os pontos finais privados só podem ser criados para novos cofres dos Serviços de Recuperação (que não têm quaisquer itens registados no cofre). Por isso, os pontos finais privados devem ser criados antes de tentar proteger quaisquer itens para o cofre.
- Uma rede virtual pode conter pontos finais privados para vários cofres dos Serviços de Recuperação. Além disso, um cofre dos Serviços de Recuperação pode ter pontos finais privados para ele em várias redes virtuais. No entanto, o número máximo de pontos finais privados que podem ser criados para um cofre é de 12.
- Assim que um ponto final privado for criado para um cofre, o cofre será bloqueado. Não será acessível (para cópias de segurança e restauros) a partir de redes para além das que contêm um ponto final privado para o cofre. Se todos os pontos finais privados do cofre forem removidos, o cofre estará acessível a partir de todas as redes.
- Uma ligação de ponto final privado para cópia de segurança utiliza um total de 11 IPs privados na sua sub-rede, incluindo os utilizados pela Azure Backup para armazenamento. Este número pode ser superior (até 25) para certas regiões de Azure. Por isso, sugerimos que tenha iPs privados suficientes disponíveis quando tentar criar pontos finais privados para backup.
- Enquanto um cofre de Serviços de Recuperação é usado por (ambos) Azure Backup e Azure Site Recovery, este artigo discute o uso de pontos finais privados apenas para Azure Backup.
- O Azure Ative Directory não suporta atualmente pontos finais privados. Assim, os IPs e FQDNs necessários para que o Azure Ative Directory funcione numa região terá de ser autorizado a aceder à saída da rede segura ao realizar cópias de segurança de bases de dados em VMs Azure e cópia de segurança utilizando o agente MARS. Também pode utilizar tags NSG e Azure Firewall para permitir o acesso ao Azure AD, conforme aplicável.
- As redes virtuais com políticas de rede não são suportadas por pontos finais privados. Terá de [desativar](../private-link/disable-private-endpoint-network-policy.md) a Polícia de Rede antes de continuar.
- Tem de voltar a registar o fornecedor de recursos dos Serviços de Recuperação com a assinatura se o registar antes de 1 de maio de 2020. Para voltar a registar o fornecedor, aceda à sua subscrição no portal Azure, navegue para o **fornecedor de Recursos** na barra de navegação esquerda, em seguida, selecione **Microsoft.RecoveryServices** e selecione **Re-registr .**
- [A restauração transversal](backup-create-rs-vault.md#set-cross-region-restore) para as cópias de dados SQL e SAP HANA não são suportadas se o cofre tiver pontos finais privados ativados.
- Quando você mover um cofre de Serviços de Recuperação já usando pontos finais privados para um novo inquilino, você precisará atualizar o cofre dos Serviços de Recuperação para recriar e reconfigurar a identidade gerida do cofre e criar novos pontos finais privados conforme necessário (que deve estar no novo inquilino). Se isto não for feito, as operações de backup e restauro começarão a falhar. Além disso, quaisquer permissões de controlo de acesso baseadas em funções (RBAC) criadas dentro da subscrição terão de ser reconfiguradas.

## <a name="recommended-and-supported-scenarios"></a>Cenários recomendados e apoiados

Enquanto os pontos finais privados estão habilitados para o cofre, eles são usados para backup e restauro de cargas de cargas DE SQL e SAP HANA apenas em um backup de VM e agente MARS Azure. Pode utilizar o cofre para cópias de segurança de outras cargas de trabalho também (no entanto, não exigirão pontos finais privados). Além da cópia de segurança das cargas de trabalho DA SQL e SAP HANA e da cópia de segurança utilizando o agente MARS, os pontos finais privados também são utilizados para realizar a recuperação de ficheiros para a cópia de segurança do Azure VM. Para mais informações, consulte a seguinte tabela:

| Backup de cargas de trabalho em Azure VM (SQL, SAP HANA), Backup usando o Agente MARS | Recomenda-se a utilização de pontos finais privados para permitir a cópia de segurança e a restauração sem necessidade de adicionar a uma lista de permitis quaisquer IPs/FQDNs para Azure Backup ou Azure Storage a partir das suas redes virtuais. Nesse cenário, certifique-se de que os VMs que acolhem bases de dados SQL podem chegar a IPs AD ou FQDNs Azure. |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| **Backup Azure VM**                                         | A cópia de segurança VM não requer que permita o acesso a quaisquer IPs ou FQDNs. Portanto, não requer pontos finais privados para cópia de segurança e restauro de discos.  <br><br>   No entanto, a recuperação de ficheiros de um cofre contendo pontos finais privados seria restrita a redes virtuais que contenham um ponto final privado para o cofre. <br><br>    Ao utilizar discos não geridos da ACL, certifique-se de que a conta de armazenamento que contém os discos permite o acesso a **serviços de confiança** da Microsoft se for ACL'ed. |
| **Backup de Ficheiros Azure**                                      | As cópias de segurança do Azure Files são armazenadas na conta de armazenamento local. Portanto, não requer pontos finais privados para apoio e restauro. |

## <a name="get-started-with-creating-private-endpoints-for-backup"></a>Começar com a criação de pontos finais privados para backup

As secções seguintes discutem os passos envolvidos na criação e utilização de pontos finais privados para o Azure Backup dentro das suas redes virtuais.

>[!IMPORTANT]
> É altamente recomendável que siga os passos na mesma sequência que mencionado neste documento. Se não o fizer, o cofre pode tornar-se incompatível com a utilização de pontos finais privados e exigir que reinicie o processo com um novo cofre.

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação 

Os pontos finais privados de Backup só podem ser criados para cofres dos Serviços de Recuperação que não tenham quaisquer itens protegidos (ou não tenham tido quaisquer itens tentados a ser protegidos ou registados no passado). Por isso, sugerimos que crie um novo cofre para começar. Para obter mais informações sobre a criação de um novo cofre, consulte [Criar e configurar um cofre dos Serviços de Recuperação.](backup-create-rs-vault.md)

Consulte [esta secção](#create-a-recovery-services-vault-using-the-azure-resource-manager-client) para aprender a criar um cofre utilizando o cliente Azure Resource Manager. Isto cria um cofre com a sua identidade gerida já ativada.

## <a name="enable-managed-identity-for-your-vault"></a>Ativar identidade gerida para o seu cofre

Identidades geridas permitem que o cofre crie e utilize pontos finais privados. Esta secção fala em permitir a identidade gerida para o seu cofre.

1. Vá ao cofre dos Serviços de Recuperação -> **Identidade.**

    ![Alterar o estado de identidade para On](./media/private-endpoints/identity-status-on.png)

1. Alterar o **Estado** para **On** e selecionar **Guardar**.

1. Um **ID de objeto** é gerado, que é a identidade gerida do cofre.

    >[!NOTE]
    >Uma vez ativado, a Identidade Gerida **não** deve ser desativada (mesmo temporariamente). Desativar a identidade gerida pode levar a um comportamento inconsistente.

## <a name="grant-permissions-to-the-vault-to-create-required-private-endpoints"></a>Conceder permissões ao cofre para criar pontos finais privados necessários

Para criar os pontos finais privados necessários para o Azure Backup, o cofre (a Identidade Gerida do cofre) deve ter permissões para os seguintes grupos de recursos:

- O Grupo de Recursos que contém o VNet alvo
- O Grupo de Recursos onde os Pontos Finais Privados devem ser criados
- O Grupo de Recursos que contém as zonas privadas de DNS, como discutido em detalhe [aqui](#create-private-endpoints-for-azure-backup)

Recomendamos que conceda o papel **de Contribuinte** para esses três grupos de recursos ao cofre (identidade gerida). Os seguintes passos descrevem como fazê-lo para um determinado grupo de recursos (isto tem de ser feito para cada um dos três grupos de recursos):

1. Vá ao Grupo de Recursos e navegue para **Access Control (IAM)** na barra esquerda.
1. Uma vez no **Controlo de Acesso,** vá **adicionar uma atribuição de função**.

    ![Adicionar uma atribuição de função](./media/private-endpoints/add-role-assignment.png)

1. No painel de atribuição de **funções Add,** escolha **o Contribuinte** como **Função,** e use o **Nome** do cofre como **Principal**. Selecione o seu cofre e selecione **Guarde** quando terminar.

    ![Escolha o papel e o principal](./media/private-endpoints/choose-role-and-principal.png)

Para gerir permissões a um nível mais granular, consulte [Criar funções e permissões manualmente.](#create-roles-and-permissions-manually)

## <a name="create-private-endpoints-for-azure-backup"></a>Criar pontos finais privados para backup Azure

Esta secção explica como criar um ponto final privado para o seu cofre.

1. Navegue até ao seu cofre criado acima e vá para **ligações privadas** na barra de navegação esquerda. Selecione **+Ponto final privado** no topo para começar a criar um novo ponto final privado para este cofre.

    ![Criar novo ponto final privado](./media/private-endpoints/new-private-endpoint.png)

1. Uma vez no processo **Create Private Endpoint,** será necessário especificar detalhes para criar a sua ligação de ponto final privado.
  
    1. **Fundamentos**: Preencha os detalhes básicos para os seus pontos finais privados. A região deve ser a mesma que o cofre e o recurso a ser apoiado.

        ![Preencha detalhes básicos](./media/private-endpoints/basics-tab.png)

    1. **Recurso**: Este separador requer que selecione o recurso PaaS para o qual pretende criar a sua ligação. Selecione **Microsoft.RecoveryServices/vaults** do tipo de recurso para a subscrição desejada. Uma vez feito, escolha o nome do seu cofre de Serviços de Recuperação como **recurso** e **AzureBackup** como **sub-recurso target**.

        ![Selecione o recurso para a sua ligação](./media/private-endpoints/resource-tab.png)

    1. **Configuração**: Na configuração, especifique a rede virtual e a sub-rede onde pretende que o ponto final privado seja criado. Este será o Vnet onde o VM está presente.

        Para se ligar em privado, precisa de registos DNS necessários. Com base na configuração da sua rede, pode escolher um dos seguintes:

          - Integre o seu ponto final privado com uma zona privada de DNS: Selecione **Sim** se desejar integrar.
          - Utilize o seu servidor DNS personalizado: Selecione **Não** se desejar utilizar o seu próprio servidor DNS.

        A gestão dos registos DNS para ambos são [descritos mais tarde](#manage-dns-records).

          ![Especificar a rede virtual e a sub-rede](./media/private-endpoints/configuration-tab.png)

    1. Opcionalmente, pode adicionar **Tags** para o seu ponto final privado.
    1. Continue a **rever + criar** uma vez feito a introdução de detalhes. Quando a validação estiver concluída, selecione **Criar** para criar o ponto final privado.

## <a name="approve-private-endpoints"></a>Aprovar pontos finais privados

Se o utilizador que cria o ponto final privado for também o proprietário do cofre dos Serviços de Recuperação, o ponto final privado acima criado será aprovado automaticamente. Caso contrário, o proprietário do cofre deve aprovar o ponto final privado antes de poder usá-lo. Esta secção discute a aprovação manual de pontos finais privados através do portal Azure.

Consulte [a aprovação manual de pontos finais privados utilizando o Cliente Gestor de Recursos Azure](#manual-approval-of-private-endpoints-using-the-azure-resource-manager-client) para utilizar o cliente Azure Resource Manager para a aprovação de pontos finais privados.

1. No cofre dos Serviços de Recuperação, navegue para **ligações privadas** no bar esquerdo.
1. Selecione a ligação de ponto final privado que deseja aprovar.
1. Selecione **Aprovar** na barra superior. Também pode selecionar **Rejeitar** ou **Remover** se pretender rejeitar ou eliminar a ligação ponto final.

    ![Aprovar pontos finais privados](./media/private-endpoints/approve-private-endpoints.png)

## <a name="manage-dns-records"></a>Gerir registos DNS

Como descrito anteriormente, precisa dos registos DNS necessários nas suas zonas ou servidores DNS privados para se ligar em privado. Pode integrar o seu ponto final privado diretamente com as zonas de DNS privadas do Azure ou utilizar os seus servidores DNS personalizados para o conseguir, com base nas suas preferências de rede. Isto terá de ser feito para os três serviços: Backup, Blobs e Queues.

### <a name="when-integrating-private-endpoints-with-azure-private-dns-zones"></a>Ao integrar pontos finais privados com zonas privadas de DNS do Azure

Se optar por integrar o seu ponto final privado com zonas privadas de DNS, o Backup adicionará os registos DNS necessários. Pode ver as zonas privadas de DNS que estão a ser utilizadas na **configuração DNS** do ponto final privado. Se estas zonas de DNS não estiverem presentes, serão criadas automaticamente ao criar o ponto final privado. No entanto, deve verificar se a sua rede virtual (que contém os recursos a apoiar) está devidamente ligada às três zonas privadas de DNS, conforme descrito abaixo.

![Configuração DNS na zona privada de DNS do Azure](./media/private-endpoints/dns-configuration.png)

#### <a name="validate-virtual-network-links-in-private-dns-zones"></a>Validar links de rede virtuais em zonas privadas de DNS

Para cada zona **privada de DNS** listada acima (para backup, blobs e filas), faça o seguinte:

1. Navegue para a opção de **links de rede Virtual** na barra de navegação esquerda.
1. Você deve ser capaz de ver uma entrada para a rede virtual para a qual você criou o ponto final privado, como o mostrado abaixo:

    ![Rede virtual para ponto final privado](./media/private-endpoints/virtual-network-links.png)

1. Se não vir uma entrada, adicione uma ligação de rede virtual a todas as zonas DNS que não as têm.

    ![Adicionar link de rede virtual](./media/private-endpoints/add-virtual-network-link.png)

### <a name="when-using-custom-dns-server-or-host-files"></a>Ao utilizar ficheiros de servidor DNS personalizados ou de anfitriões

Se estiver a utilizar os seus servidores DNS personalizados, terá de criar as zonas DNS necessárias e adicionar os registos DNS necessários pelos pontos finais privados aos seus servidores DNS. Para bolhas e filas, também pode utilizar reencaminhadores condicionais.

#### <a name="for-the-backup-service"></a>Para o serviço de backup

1. No seu servidor DNS, crie uma zona DNS para Cópia de Segurança de acordo com a seguinte convenção de nomeação:

    |Zona |Serviço |
    |---------|---------|
    |`privatelink.<geo>.backup.windowsazure.com`   |  Backup        |

    >[!NOTE]
    > No texto acima referido, `<geo>` refere-se ao código da região (por *exemplo, eus* e *ne* para os EUA orientais e norte da Europa, respectivamente). Consulte as seguintes listas para códigos de regiões:
    >
    > - [Todas as nuvens públicas](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/AzureRegionCodesList.docx)
    > - [China](/azure/china/resources-developer-guide#check-endpoints-in-azure)
    > - [Alemanha](../germany/germany-developer-guide.md#endpoint-mapping)
    > - [US Gov](../azure-government/documentation-government-developer-guide.md)

1. Em seguida, precisamos adicionar os registos DNS necessários. Para visualizar os registos que precisam de ser adicionados à zona de BACKUP DNS, navegue até ao ponto final privado que criou acima e vá para a opção **de configuração DNS** sob a barra de navegação esquerda.

    ![Configuração DNS para servidor DNS personalizado](./media/private-endpoints/custom-dns-configuration.png)

1. Adicione uma entrada para cada FQDN e IP apresentados como registos tipo A na sua zona DE DNS para cópia de segurança. Se estiver a utilizar um ficheiro anfitrião para resolução de nomes, faça as entradas correspondentes no ficheiro anfitrião para cada IP e FQDN de acordo com o seguinte formato:

    `<private ip><space><backup service privatelink FQDN>`

>[!NOTE]
>Como mostrado na imagem acima, as FQDNs retratam `xxxxxxxx.<geo>.backup.windowsazure.com` e não `xxxxxxxx.privatelink.<geo>.backup. windowsazure.com` . Nesses casos, certifique-se de que inclui (e, se necessário, adicione) o `.privatelink.` de acordo com o formato indicado.

#### <a name="for-blob-and-queue-services"></a>Para serviços blob e fila

Para bolhas e filas, pode utilizar reencaminhadores condicional ou criar zonas DNS no seu servidor DNS.

##### <a name="if-using-conditional-forwarders"></a>Se utilizar reencaminhadores condicionais

Se estiver a utilizar os reencaminhadores condicional, adicione os reencaminhadores para as FQDNs blob e filas da seguinte forma:

|FQDN  |IP  |
|---------|---------|
|`privatelink.blob.core.windows.net`     |  168.63.129.16       |
|`privatelink.queue.core.windows.net`     | 168.63.129.16        |

##### <a name="if-using-private-dns-zones"></a>Se utilizar zonas privadas de DNS

Se estiver a utilizar zonas DNS para bolhas e filas, terá primeiro de criar estas zonas DNS e, mais tarde, adicionar os registos A necessários.

|Zona |Serviço  |
|---------|---------|
|`privatelink.blob.core.windows.net`     |  Blobs     |
|`privatelink.queue.core.windows.net`     | Fila        |

Neste momento, só criaremos as zonas para bolhas e filas quando usarmos servidores DNS personalizados. A adição de registos DNS será feita mais tarde em dois passos:

1. Quando regista a primeira instância de backup, isto é, quando configurar o backup pela primeira vez
1. Quando fizer o primeiro backup

Vamos fazer estes passos nas seguintes secções.

## <a name="use-private-endpoints-for-backup"></a>Use pontos de apoio privados para backup

Uma vez aprovados os pontos finais privados criados para o cofre no seu VNet, pode começar a ui-los para executar as suas cópias de segurança e restauros.

>[!IMPORTANT]
>Certifique-se de que completou todos os passos acima mencionados no documento com sucesso antes de prosseguir. Para recapitular, deve ter completado os passos na seguinte lista de verificação:
>
>1. Criou um cofre (novo) serviços de recuperação
>2. Permitiu que o cofre usasse o sistema atribuído Identidade Gerida
>3. Permissões relevantes atribuídas à Identidade Gerida do cofre
>4. Criei um Ponto Final Privado para o seu cofre
>5. Aprovado o Ponto Final Privado (se não for aprovado automaticamente)
>6. Assegurado que todos os registos DNS são adequadamente adicionados (exceto registos de blob e fila para servidores personalizados, que serão discutidos nas seguintes secções)

### <a name="check-vm-connectivity"></a>Verifique a conectividade VM

No VM na rede bloqueada, certifique-se de que:

1. O VM deve ter acesso à AAD.
2. Execute **nslookup** no URL de backup `xxxxxxxx.privatelink.<geo>.backup. windowsazure.com` () do seu VM, para garantir a conectividade. Isto deve devolver o IP privado atribuído na sua rede virtual.

### <a name="configure-backup"></a>Configurar a cópia de segurança

Uma vez assegurada a lista de verificação acima e o acesso a ter sido concluído com sucesso, pode continuar a configurar a cópia de segurança das cargas de trabalho para o cofre. Se estiver a utilizar um servidor DNS personalizado, terá de adicionar entradas DNS para bolhas e filas que estejam disponíveis após configurar a primeira cópia de segurança.

#### <a name="dns-records-for-blobs-and-queues-only-for-custom-dns-servershost-files-after-the-first-registration"></a>Registos DNS para bolhas e filas (apenas para servidores DNS/ficheiros anfitriões personalizados) após o primeiro registo

Depois de configurar a cópia de segurança para pelo menos um recurso num cofre privado ativado, adicione os registos DNS necessários para bolhas e filas, conforme descrito abaixo.

1. Navegue para o seu Grupo de Recursos e procure o ponto final privado que criou.
1. Além do nome privado dado por si, verá mais dois pontos finais privados a serem criados. Estes começam `<the name of the private endpoint>_ecs` com e são sufixados com `_blob` `_queue` e, respectivamente.

    ![Recursos privados de ponto final](./media/private-endpoints/private-endpoint-resources.png)

1. Navegue para cada um destes pontos finais privados. Na opção de configuração DE DNS para cada um dos dois pontos finais privados, você verá um registo com e um FQDN e um endereço IP. Adicione ambos ao seu servidor DNS personalizado, além dos descritos anteriormente.
Se estiver a utilizar um ficheiro anfitrião, faça as entradas correspondentes no ficheiro anfitrião para cada IP/FQDN de acordo com o seguinte formato:

    `<private ip><space><blob service privatelink FQDN>`<br>
    `<private ip><space><queue service privatelink FQDN>`

    ![Configuração blob DNS](./media/private-endpoints/blob-dns-configuration.png)

Além do acima, há outra entrada necessária após o primeiro backup, que é discutido [mais tarde](#dns-records-for-blobs-only-for-custom-dns-servershost-files-after-the-first-backup).

### <a name="backup-and-restore-of-workloads-in-azure-vm-sql-and-sap-hana"></a>Cópia de segurança e restauro de cargas de trabalho em Azure VM (SQL e SAP HANA)

Uma vez que o ponto final privado é criado e aprovado, não são necessárias outras alterações do lado do cliente para usar o ponto final privado (a menos que você esteja usando SQL Availability Groups, que discutimos mais tarde nesta secção). Toda a comunicação e transferência de dados da sua rede segura para o cofre será realizada através do ponto final privado. No entanto, se remover pontos finais privados para o cofre depois de um servidor (SQL ou SAP HANA) ter sido registado no seu registo, terá de voltar a registar o contentor com o cofre. Não precisas de parar de proteção para eles.

#### <a name="dns-records-for-blobs-only-for-custom-dns-servershost-files-after-the-first-backup"></a>Registos DNS para blobs (apenas para servidores DNS/ficheiros anfitriões personalizados) após a primeira cópia de segurança

Depois de executar a primeira cópia de segurança e estiver a utilizar um servidor DNS personalizado (sem encaminhamento condicional), é provável que a sua cópia de segurança falhe. Se isso acontecer:

1. Navegue para o seu Grupo de Recursos e procure o ponto final privado que criou.
1. Além dos três pontos finais privados discutidos anteriormente, você verá agora um quarto ponto final privado com o seu nome começando `<the name of the private endpoint>_prot` com e são sufocados com `_blob` .

    ![Endpoing privado com sufixo "prot"](./media/private-endpoints/private-endpoint-prot.png)

1. Navegue para este novo ponto final privado. Na opção de configuração DNS, verá um registo com um FQDN e um endereço IP. Adicione estes ao seu servidor DNS privado, além dos descritos anteriormente.

    Se estiver a utilizar um ficheiro anfitrião, faça as entradas correspondentes no ficheiro anfitrião para cada IP e FQDN de acordo com o seguinte formato:

    `<private ip><space><blob service privatelink FQDN>`

>[!NOTE]
>Neste ponto, você deve ser capaz de executar **nslookup** a partir do VM e resolver para endereços IP privados quando feito nos URLs de backup e armazenamento do cofre.

### <a name="when-using-sql-availability-groups"></a>Ao utilizar grupos de disponibilidade SQL

Ao utilizar os Grupos de Disponibilidade SQL (AG), terá de providenciar o reencaminhamento condicional no DNS AG personalizado, conforme descrito abaixo:

1. Inscreva-se no seu controlador de domínio.
1. Sob a aplicação DNS, adicione os reencaminhadores condicional para as três zonas DNS (Backup, Blobs e Queues) ao anfitrião IP 168.63.129.16 ou ao endereço IP do servidor DNS personalizado, se necessário. As imagens que se seguem mostram quando está a encaminhar-se para o IP do anfitrião Azure. Se estiver a utilizar o seu próprio servidor DNS, substitua-o pelo IP do seu servidor DNS.

    ![Reencaminhadores condicionais no DNS Manager](./media/private-endpoints/dns-manager.png)

    ![Novo avançado condicional](./media/private-endpoints/new-conditional-forwarder.png)

### <a name="backup-and-restore-through-mars-agent"></a>Backup e restauro através do Agente MARS

Ao utilizar o Agente MARS para fazer o back up dos seus recursos no local, certifique-se de que a sua rede no local (contendo os seus recursos a serem apoiados) é espremiada com o Azure VNet que contém um ponto final privado para o cofre, para que possa usá-lo. Em seguida, pode continuar a instalar o agente MARS e configurar a cópia de segurança conforme detalhado aqui. No entanto, deve garantir que todas as comunicações de backup acontecem apenas através da rede de esprevação.

Mas se remover os pontos finais privados do cofre depois de um agente da MARS ter sido registado, terá de voltar a registar o contentor com o cofre. Não precisas de parar de proteção para eles.

## <a name="deleting-private-endpoints"></a>Apagar Pontos Finais Privados

Consulte [esta secção](/rest/api/virtualnetwork/privateendpoints/delete) para saber como eliminar Os Pontos Finais Privados.

## <a name="additional-topics"></a>Tópicos adicionais

### <a name="create-a-recovery-services-vault-using-the-azure-resource-manager-client"></a>Criar um cofre de Serviços de Recuperação utilizando o cliente Azure Resource Manager

Pode criar o cofre dos Serviços de Recuperação e ativar a sua Identidade Gerida (ativando a Identidade Gerida, como veremos mais tarde) utilizando o cliente Azure Resource Manager. Uma amostra para fazer isto é partilhada abaixo:

```rest
armclient PUT /subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>?api-version=2017-07-01-preview @C:\<filepath>\MSIVault.json
```

O ficheiro JSON acima deve ter o seguinte conteúdo:

Solicitação JSON:

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
>O cofre criado neste exemplo através do cliente Azure Resource Manager já é criado com uma identidade gerida atribuída pelo sistema.

### <a name="managing-permissions-on-resource-groups"></a>Gestão de permissões em Grupos de Recursos

A Identidade Gerida para o cofre necessita de ter as seguintes permissões no grupo de recursos e na rede virtual onde serão criados os pontos finais privados:

- `Microsoft.Network/privateEndpoints/*` Isto é necessário para executar CRUD em pontos finais privados no grupo de recursos. Deve ser atribuído ao grupo de recursos.
- `Microsoft.Network/virtualNetworks/subnets/join/action` Isto é necessário na rede virtual onde o IP privado está a ser anexado ao ponto final privado.
- `Microsoft.Network/networkInterfaces/read` Isto é necessário no grupo de recursos para obter a interface de rede criada para o ponto final privado.
- Função contribuinte privada da zona de DNS Esta função já existe e pode ser usada para fornecer `Microsoft.Network/privateDnsZones/A/*` e `Microsoft.Network/privateDnsZones/virtualNetworkLinks/read` permissões.

Pode utilizar um dos seguintes métodos para criar funções com permissões necessárias:

#### <a name="create-roles-and-permissions-manually"></a>Criar funções e permissões manualmente

Crie os seguintes ficheiros JSON e utilize o comando PowerShell no final da secção para criar funções:

PrivateEndpointContributorRoleDef.jsem

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

NetworkInterfaceReaderRoleDef.jsem

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

PrivateEndpointSubnetContributorRoleDef.jsem

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

    ![Selecione o ficheiro upload na janela PowerShell](./media/private-endpoints/upload-file-in-powershell.png)

1. Faça upload do seguinte script: [VaultMsiPrereqScript](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/VaultMsiPrereqScript.ps1)

1. Vá à sua pasta de casa (por exemplo: `cd /home/user` )

1. Execute o seguintes script:

    ```azurepowershell
    ./VaultMsiPrereqScript.ps1 -subscription <subscription-Id> -vaultPEResourceGroup <vaultPERG> -vaultPESubnetResourceGroup <subnetRG> -vaultMsiName <msiName>
    ```

    Estes são os parâmetros:

    - **subscrição**: **Subscrição que tem o grupo de recursos onde o ponto final privado para o cofre deve ser criado e a sub-rede onde o ponto final privado do cofre será anexado

    - **vaultPEResourceGroup**: Grupo de recursos onde será criado o ponto final privado para o cofre

    - **vaultPESubnetResourceGroup**: Grupo de recursos da sub-rede a que o ponto final privado será associado

    - **vaultMsiName**: Nome do MSI do cofre, que é o mesmo que **o Nome do Cofre**

1. Complete a autenticação e o script terá o contexto da subscrição fornecida acima. Criará os papéis apropriados se faltarem ao inquilino e atribuirá papéis à MSI do cofre.

### <a name="creating-private-endpoints-using-azure-powershell"></a>Criação de pontos finais privados usando Azure PowerShell

#### <a name="auto-approved-private-endpoints"></a>Pontos finais privados aprovados automaticamente

```azurepowershell
$vault = Get-AzRecoveryServicesVault `
        -ResourceGroupName $vaultResourceGroupName `
        -Name $vaultName
  
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
        -Name $privateEndpointConnectionName `
        -PrivateLinkServiceId $vault.ID `
        -GroupId "AzureBackup"  

$vnet = Get-AzVirtualNetwork -Name $vnetName -ResourceGroupName $VMResourceGroupName
$subnet = $vnet | Select -ExpandProperty subnets | Where-Object {$_.Name -eq '<subnetName>'}


$privateEndpoint = New-AzPrivateEndpoint `
        -ResourceGroupName $vmResourceGroupName `
        -Name $privateEndpointName `
        -Location $location `
        -Subnet $subnet `
        -PrivateLinkServiceConnection $privateEndpointConnection `
        -Force
```

### <a name="manual-approval-of-private-endpoints-using-the-azure-resource-manager-client"></a>Aprovação manual de pontos finais privados utilizando o Cliente Gestor de Recursos Azure

1. Utilize **o GetVault** para obter o ID de Ligação de Pontos De Final privado para o seu ponto final privado.

    ```rest
    armclient GET /subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/vaults/<vaultname>?api-version=2017-07-01-preview
    ```

    Isto devolverá o ID de Ligação De Ponto Final Privado. O nome da ligação pode ser recuperado utilizando a primeira parte do ID de ligação da seguinte forma:

    `privateendpointconnectionid = {peName}.{vaultId}.backup.{guid}`

1. Obtenha o **ID de Ligação De Ponto Final Privado** (e o Nome de Ponto Final **Privado,** sempre que necessário) da resposta e substitua-o no seguinte JSON e Azure Resource Manager URI e tente alterar o Estado para "Aprovado/Rejeitado/Desligado", como demonstrado na amostra abaixo:

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

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

P. Posso criar um ponto final privado para um cofre de reserva existente?<br>
A. Não, os pontos finais privados só podem ser criados para novos cofres de reserva. Então o cofre nunca deve ter protegido nenhum objeto. Na verdade, nenhuma tentativa de proteger quaisquer itens no cofre pode ser feita antes de criar pontos finais privados.

P. Tentei proteger um objeto para o meu cofre, mas falhou e o cofre ainda não contém nenhum objeto protegido. Posso criar pontos finais privados para este cofre?<br>
A. Não, o cofre não deve ter tido nenhuma tentativa de proteger quaisquer objetos no passado.

P. Tenho um cofre que está a usar pontos finais privados para reforços e restauros. Posso adicionar ou remover mais tarde pontos finais privados para este cofre, mesmo que eu tenha itens de reserva protegidos?<br>
A. Sim. Se já criou pontos finais privados para um cofre e itens de reserva protegidos, pode adicionar ou remover os pontos finais privados conforme necessário.

P. O ponto final privado do Azure Backup também pode ser usado para a recuperação do local de Azure?<br>
A. Não, o ponto final privado para cópia de segurança só pode ser utilizado para o Azure Backup. Terá de criar um novo ponto final privado para a Recuperação do Site Azure, se for suportado pelo serviço.

P. Perdi um dos passos deste artigo e fui proteger a minha fonte de dados. Ainda posso usar pontos finais privados?<br>
A. Não seguir os passos do artigo e continuar a proteger os itens pode levar a que o cofre não possa utilizar pontos finais privados. Por isso, recomenda-se que consulte esta lista de verificação antes de continuar a proteger os itens.

P. Posso usar o meu próprio servidor DNS em vez de usar a zona de DNS privada Azure ou uma zona de DNS privada integrada?<br>
A. Sim, podes usar os teus próprios servidores DNS. No entanto, certifique-se de que todos os registos DNS necessários são adicionados como sugerido nesta secção.

P. Preciso de fazer mais passos no meu servidor depois de ter seguido o processo neste artigo?<br>
A. Depois de seguir o processo detalhado neste artigo, não precisa de fazer trabalho adicional para usar pontos finais privados para fazer backup e restauro.

## <a name="next-steps"></a>Passos seguintes

- Leia sobre todos os [recursos de segurança no Azure Backup](security-overview.md).