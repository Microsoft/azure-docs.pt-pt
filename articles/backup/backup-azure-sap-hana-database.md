---
title: Faça o backup de uma base de dados SAP HANA para Azure com Azure Backup
description: Neste artigo, aprenda a fazer backup de uma base de dados SAP HANA para máquinas virtuais Azure com o serviço Azure Backup.
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: e7735c4240529cc6fc9bb6470934dd335d22aa77
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101719615"
---
# <a name="back-up-sap-hana-databases-in-azure-vms"></a>Fazer cópias de segurança de bases de dados SAP HANA nas VMs do Azure

As bases de dados SAP HANA são cargas de trabalho críticas que requerem um objetivo de baixo ponto de recuperação (RPO) e retenção a longo prazo. Pode fazer backup das bases de dados SAP HANA em funcionamento em máquinas virtuais Azure (VMs) utilizando [o Azure Backup](backup-overview.md).

Este artigo mostra como fazer backup das bases de dados SAP HANA que estão a funcionar em VMs Azure para um cofre dos Serviços de Recuperação de Backup Azure.

Neste artigo, vai aprender a:
> [!div class="checklist"]
>
> * Criar e configurar um cofre
> * Descubra bases de dados
> * Configurar backups
> * Executar um trabalho de reserva a pedido

>[!NOTE]
>A partir de 1 de agosto de 2020, o backup SAP HANA para a RHEL (7.4, 7.6, 7,7 & 8.1) está geralmente disponível.

>[!NOTE]
>**A exclusão suave para o servidor SQL em Azure VM e a eliminação suave para as cargas de trabalho SAP HANA em Azure VM** já está disponível na pré-visualização.<br>
>Para se inscrever para a pré-visualização, escreva-nos em [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) .

## <a name="prerequisites"></a>Pré-requisitos

Consulte os [pré-requisitos](tutorial-backup-sap-hana-db.md#prerequisites) e o [que o script de pré-registo faz](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) para configurar a base de dados para obter cópia de segurança.

### <a name="establish-network-connectivity"></a>Estabelecer conectividade de rede

Para todas as operações, uma base de dados SAP HANA em execução num Azure VM requer conectividade ao serviço de Backup Azure, Azure Storage e Azure Ative Directory. Isto pode ser conseguido utilizando pontos finais privados ou permitindo o acesso aos endereços IP públicos necessários ou FQDNs. Não permitir a conectividade adequada aos serviços Azure necessários pode levar a falhas em operações como a descoberta de bases de dados, configurar backup, realizar backups e restaurar dados.

A tabela a seguir enumera as várias alternativas que pode utilizar para estabelecer conectividade:

| **Opção**                        | **Vantagens**                                               | **Desvantagens**                                            |
| --------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Pontos finais privados                 | Permitir cópias de segurança sobre iPs privados dentro da rede virtual  <br><br>   Fornecer controlo granular no lado da rede e do cofre | Incorre [nos custos](https://azure.microsoft.com/pricing/details/private-link/) padrão do ponto final privado |
| Etiquetas de serviço NSG                  | Mais fácil de gerir à medida que as mudanças de alcance são automaticamente fundidas   <br><br>   Sem custos adicionais | Pode ser usado apenas com NSGs  <br><br>    Fornece acesso a todo o serviço |
| Tags FQDN de Firewall Azure Firewall          | Mais fácil de gerir uma vez que os FQDNs necessários são geridos automaticamente | Pode ser usado apenas com Azure Firewall                         |
| Permitir o acesso ao serviço FQDNs/IPs | Sem custos adicionais   <br><br>  Funciona com todos os aparelhos de segurança da rede e firewalls | Um conjunto alargado de IPs ou FQDNs pode ser obrigado a ser acedido   |
| Utilizar um proxy HTTP                 | Ponto único de acesso à Internet aos VMs                       | Custos adicionais para executar um VM com o software proxy         |

Mais detalhes sobre a utilização destas opções são partilhados abaixo:

#### <a name="private-endpoints"></a>Pontos finais privados

Os pontos finais privados permitem-lhe ligar-se de forma segura a partir de servidores dentro de uma rede virtual ao cofre dos Serviços de Recuperação. O ponto final privado utiliza um IP a partir do espaço de endereço VNET para o seu cofre. O tráfego de rede entre os seus recursos dentro da rede virtual e o cofre viaja pela sua rede virtual e uma ligação privada na rede de espinha dorsal da Microsoft. Isto elimina a exposição da internet pública. Leia mais sobre os pontos finais privados para o Azure Backup [aqui](./private-endpoints.md).

#### <a name="nsg-tags"></a>Etiquetas NSG

Se utilizar grupos de segurança de rede (NSG), utilize a etiqueta de serviço *AzureBackup* para permitir o acesso de saída ao Azure Backup. Além da etiqueta Azure Backup, também precisa de permitir a conectividade para a autenticação e transferência de dados, criando [regras de NSG semelhantes](../virtual-network/network-security-groups-overview.md#service-tags) para Azure *AD (AzureActiveDirectory)* e Azure Storage *(Armazenamento).*  Os seguintes passos descrevem o processo para criar uma regra para a etiqueta de backup Azure:

1. Em **Todos os Serviços,** vá aos **grupos de segurança da Rede** e selecione o grupo de segurança da rede.

1. Selecione **regras de segurança de saída** em **Definições**.

1. Selecione **Adicionar**. Introduza todos os detalhes necessários para a criação de uma nova regra, conforme descrito nas [definições de regras de segurança](../virtual-network/manage-network-security-group.md#security-rule-settings). Certifique-se de que a opção **Destino** está definida para tag de serviço *de serviço* e de **destino** está definida para *AzureBackup*.

1. **Selecione Adicionar** para salvar a regra de segurança de saída recém-criada.

Pode igualmente criar regras de segurança de saída NSG para Azure Storage e Azure AD. Para obter mais informações sobre etiquetas de serviço, consulte [este artigo.](../virtual-network/service-tags-overview.md)

#### <a name="azure-firewall-tags"></a>Tags Azure Firewall

Se estiver a utilizar o Azure Firewall, crie uma regra de aplicação utilizando a [etiqueta FQDN Azure Firewall AzureBackup .](../firewall/fqdn-tags.md)  Isto permite que todo o acesso de saída ao Azure Backup.

#### <a name="allow-access-to-service-ip-ranges"></a>Permitir o acesso às gamas IP de serviço

Se optar por permitir iPs de serviço de acesso, consulte os intervalos IP no ficheiro JSON disponível [aqui](https://www.microsoft.com/download/confirmation.aspx?id=56519). Terá de permitir o acesso aos IPs correspondentes ao Azure Backup, Azure Storage e Azure Ative Directory.

#### <a name="allow-access-to-service-fqdns"></a>Permitir o acesso ao serviço FQDNs

Também pode utilizar os seguintes FQDNs para permitir o acesso aos serviços necessários a partir dos seus servidores:

| Serviço    | Nomes de domínio a serem acedidos                             |
| -------------- | ------------------------------------------------------------ |
| Reserva Azure  | `*.backup.windowsazure.com`                             |
| Armazenamento Azure | `*.blob.core.windows.net` <br><br> `*.queue.core.windows.net` |
| Azure AD      | Permitir o acesso às FQDNs nos termos das secções 56 e 59 de acordo com [este artigo](/office365/enterprise/urls-and-ip-address-ranges#microsoft-365-common-and-office-online) |

#### <a name="use-an-http-proxy-server-to-route-traffic"></a>Use um servidor de procuração HTTP para encaminhar o tráfego

Quando faz backup de uma base de dados SAP HANA em execução num Azure VM, a extensão de backup no VM utiliza as APIs HTTPS para enviar comandos de gestão para Azure Backup e dados para Azure Storage. A extensão de backup também utiliza Azure AD para autenticação. Encaminhe o tráfego da extensão da cópia de segurança destes três serviços através do proxy HTTP. Utilize a lista de IPs e FQDNs acima mencionadas para permitir o acesso aos serviços necessários. Os servidores de procuração autenticados não são suportados.

> [!NOTE]
> Não existe suporte de procuração de nível de serviço. Ou seja, o tráfego através do proxy de apenas alguns ou serviços selecionados (serviços de backup Azure) não é suportado. Todos os dados ou tráfego podem ser encaminhados por procuração ou não.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-the-databases"></a>Descubra as bases de dados

1. No cofre, em **"Getting Started",** selecione **Backup**. Em Onde está a correr a sua carga **de trabalho?**
2. Selecione **Iniciar a Descoberta**. Isto inicia a descoberta de VMs Linux desprotegidos na região do cofre.

   * Após a descoberta, os VMs desprotegidos aparecem no portal, listados pelo nome e pelo grupo de recursos.
   * Se um VM não está listado como esperado, verifique se já está num cofre.
   * Vários VMs podem ter o mesmo nome, mas pertencem a diferentes grupos de recursos.

3. Em **Select Virtual Machines**, selecione o link para descarregar o script que fornece permissões para o serviço de Backup Azure para aceder aos VMs SAP HANA para a descoberta da base de dados.
4. Execute o script em cada VM que hospeda bases de dados SAP HANA que pretende fazer.
5. Depois de executar o script nos VMs, em **Select Virtual Machines,** selecione os VMs. Em seguida, **selecione Discover DBs**.
6. A Azure Backup descobre todas as bases de dados SAP HANA na VM. Durante a descoberta, a Azure Backup regista o VM com o cofre e instala uma extensão no VM. Nenhum agente está instalado na base de dados.

    ![Descubra bases de dados SAP HANA](./media/backup-azure-sap-hana-database/hana-discover.png)

## <a name="configure-backup"></a>Configurar a cópia de segurança  

Agora ative a cópia de segurança.

1. No passo 2, selecione **Configure Backup**.

    ![Configurar a Cópia de Segurança](./media/backup-azure-sap-hana-database/configure-backup.png)
2. Em **Selecionar itens para fazer o back up,** selecione todas as bases de dados que pretende proteger > **OK**.

    ![Selecione itens para fazer back-up](./media/backup-azure-sap-hana-database/select-items.png)
3. Na política **de backup** Escolha a política  >  **de backup,** crie uma nova política de backup para as bases de dados, de acordo com as instruções abaixo.

    ![Escolha a política de backup](./media/backup-azure-sap-hana-database/backup-policy.png)
4. Depois de criar a política, no menu **Backup,** selecione **Ative backup**.

    ![Ativar backup](./media/backup-azure-sap-hana-database/enable-backup.png)
5. Acompanhe o progresso da configuração de backup na área de **Notificações** do portal.

### <a name="create-a-backup-policy"></a>Criar uma política de cópias de segurança

Uma política de backup define quando os backups são levados, e quanto tempo são retidos.

* Uma política é criada ao nível do cofre.
* Vários cofres podem utilizar a mesma política de cópias de segurança, mas deve aplicar a política a cada cofre.

>[!NOTE]
>O Azure Backup não se ajusta automaticamente às alterações de horário de verão ao fazer o backup de uma base de dados SAP HANA em funcionamento num VM Azure.
>
>Modifique a apólice manualmente, se necessário.

Especificar as definições de política da seguinte forma:

1. Em **Nome da política**, introduza um nome para a nova política.

   ![Insira o nome da política](./media/backup-azure-sap-hana-database/policy-name.png)
1. Em **Política de Cópia de segurança completa**, selecione uma **Frequência de Cópia de Segurança**: escolha **Diária** ou **Semanal**.
   * **Diariamente**: Selecione a hora e o fuso horário em que o trabalho de reserva começa.
       * Tens de fazer uma cópia de segurança completa. Não pode desligar esta opção.
       * Selecione **Cópia de Segurança Completa** para ver a política.
       * Não pode criar cópias de segurança diferenciais para cópias de segurança completas diárias.
   * **Semanalmente**: Selecione o dia da semana, hora e fuso horário em que o trabalho de reserva funciona.

   ![Selecione frequência de backup](./media/backup-azure-sap-hana-database/backup-frequency.png)

1. No **Alcance de Retenção,** configurar as definições de retenção para a cópia de segurança completa.
    * Por predefinição, todas as opções são selecionadas. Limpe os limites de alcance de retenção que não quer usar, e desemote os que o faz.
    * O período mínimo de retenção para qualquer tipo de backup (completo/diferencial/log) é de sete dias.
    * Os pontos de recuperação são marcados para retenção com base no respetivo período de retenção. Por exemplo, se selecionar uma cópia de segurança completa diária, vai ser acionada apenas uma cópia de segurança completa por dia.
    * A cópia de segurança para um dia específico é marcada e mantida com base na gama e configuração semanais de retenção.
    * Os períodos de retenção mensais e anuais comportam-se de forma semelhante.

1. No menu **Política de Cópia de segurança completa**, selecione **OK** para aceitar as definições.
1. Selecione **Backup diferencial** para adicionar uma política diferencial.
1. Em **Política de Cópia de segurança diferencial**, selecione **Ativar** para abrir os controlos de frequência e retenção.
    * No máximo, pode acionar uma cópia de segurança diferencial por dia.
    * As cópias de segurança diferenciais podem ser retidas durante um máximo de 180 dias. Se precisar de uma maior retenção, deve utilizar cópias de segurança completas.

    ![Política de backup diferencial](./media/backup-azure-sap-hana-database/differential-backup-policy.png)

    > [!NOTE]
    > Pode escolher um diferencial ou um incremental como uma cópia de segurança diária, mas não ambos.
1. Na **política de Cópia de Segurança Incremental,** selecione **Ativar** para abrir os controlos de frequência e retenção.
    * No máximo, pode desencadear uma cópia de segurança incremental por dia.
    * As cópias de segurança incrementais podem ser mantidas por um máximo de 180 dias. Se precisar de uma maior retenção, deve utilizar cópias de segurança completas.

    ![Política incremental de backup](./media/backup-azure-sap-hana-database/incremental-backup-policy.png)

1. Selecione **OK** para guardar a política e voltar ao menu principal **Política de cópia de segurança**.
1. Selecione **'Registar'** para adicionar uma política de backup de registo de transações,
    * In **Log Backup**, selecione **Enable**.  Isto não pode ser desativado, uma vez que a SAP HANA gere todos os backups de registo.
    * Desacione os controlos de frequência e retenção.

    > [!NOTE]
    > As cópias de segurança de registo só começam a fluir depois de concluída uma cópia de segurança completa bem sucedida.

1. Selecione **OK** para guardar a política e voltar ao menu principal **Política de cópia de segurança**.
1. Depois de terminar de definir a política de backup, selecione **OK**.

> [!NOTE]
> Cada cópia de segurança de registo está acorrentada à cópia de segurança completa anterior para formar uma cadeia de recuperação. Este backup completo será mantido até que a retenção da última cópia de segurança de registo tenha expirado. Isto pode significar que a cópia de segurança completa é retida por um período extra para garantir que todos os registos podem ser recuperados. Vamos supor que um utilizador tem uma cópia de segurança semanal, diferencial diário e registos de 2 horas. Todos estão retidos por 30 dias. Mas, a totalidade semanal só pode ser limpa/apagada após a próxima cópia de segurança completa disponível, ou seja, após 30 + 7 dias. Por exemplo, um backup semanal completo acontece no dia 16 de novembro. De acordo com a política de retenção, deve ser mantida até 16 de dezembro. O último registo para este registo completo acontece antes do próximo dia 22 de novembro. Até que este registo esteja disponível até 22 de dezembro, o 16 de novembro completo não pode ser apagado. Assim, o 16º de novembro completo é mantido até 22 de dezembro.

## <a name="run-an-on-demand-backup"></a>Executar uma cópia de segurança a pedido

Os backups são executados de acordo com o calendário de apólices. Pode executar uma cópia de segurança a pedido da seguinte forma:

1. No menu do cofre, selecione **itens de reserva**.
2. Em **Itens de Cópia de Segurança,** selecione o VM que executa a base de dados SAP HANA e, em seguida, selecione **Backup agora**.
3. In **Backup Now,** escolha o tipo de cópia de segurança que pretende realizar. Em seguida, selecione **OK**. Esta cópia de segurança será mantida de acordo com a política associada a este item de backup.
4. Monitorize as notificações do portal. Pode monitorizar o progresso do trabalho no painel de segurança do cofre > **trabalhos de reserva**  >  **em curso**. Dependendo do tamanho da sua base de dados, a criação da cópia de segurança inicial pode demorar algum tempo.

Por padrão, a retenção de backups a pedido é de 45 dias.

## <a name="run-sap-hana-studio-backup-on-a-database-with-azure-backup-enabled"></a>Executar backup do ESTÚDIO SAP HANA numa base de dados com Azure Backup ativado

Se quiser fazer uma cópia de segurança local (usando o HANA Studio) de uma base de dados que está a ser apoiada com a Azure Backup, faça o seguinte:

1. Aguarde quaisquer cópias de segurança completas ou de registo para que a base de dados termine. Verifique o estado no ESTÚDIO SAP HANA / Cockpit.
1. Desative as cópias de segurança e desative o catálogo de cópias de segurança para o sistema de ficheiros para base de dados relevante.
1. Para isso, clique duas vezes em **configuração do sistema**  >  **Selecione** o filtro  >  **de base de dados**  >  **(Log)**.
1. Desa um **enable_auto_log_backup** para **o nº**.
1. **Desa** esta log_backup_using_backint a **Falso**.
1. **Desa** esta catalog_backup_using_backint a **Falso**.
1. Faça uma cópia de segurança total da base de dados.
1. Aguarde o fim da cópia de segurança completa e do catálogo.
1. Reverta as configurações anteriores para as do Azure:
    * Desateia **enable_auto_log_backup** para **Sim.**
    * Desa parte **log_backup_using_backint** a **True**.
    * De  catalog_backup_using_backint a **True**.

## <a name="next-steps"></a>Passos seguintes

* Saiba como restaurar as [bases de dados SAP HANA em execução em VMs Azure](./sap-hana-db-restore.md)
* Saiba como [gerir as bases de dados SAP HANA que são apoiadas através do Azure Backup](./sap-hana-db-manage.md)
