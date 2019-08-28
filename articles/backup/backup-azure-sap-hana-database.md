---
title: Fazer backup de um banco de dados SAP HANA no Azure com o backup do Azure | Microsoft Docs
description: Este tutorial explica como fazer backup de um banco de dados SAP HANA no Azure com o serviço de backup do Azure.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 08/27/2019
ms.author: dacurwin
ms.openlocfilehash: 6ac15e042f93befe406553d622c790eeabad7c2c
ms.sourcegitcommit: 388c8f24434cc96c990f3819d2f38f46ee72c4d8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/27/2019
ms.locfileid: "70060704"
---
# <a name="back-up-an-sap-hana-database-to-azure"></a>Fazer backup de um banco de dados SAP HANA no Azure

O [backup do Azure](backup-overview.md) dá suporte ao backup de bancos de dados SAP Hana no Azure.

> [!NOTE]
> Este recurso está atualmente em visualização pública. Atualmente, ele não está pronto para produção e não tem um SLA garantido.

## <a name="scenario-support"></a>Suporte a cenários

**Suporte** | **Detalhes**
--- | ---
**Áreas geográficas com suporte** | Sudeste da Austrália, Austrália oriental <br> Sul do Brasil <br> Canadá central, leste do Canadá <br> Ásia Oriental do Sul, Ásia Oriental <br> Leste dos EUA, leste dos EUA 2, Oeste EUA Central, oeste dos EUA, oeste dos EUA 2, norte EUA Central, EUA Central, Sul EUA Central<br> Índia central, sul da Índia <br> Leste do Japão, Oeste do Japão<br> Centro da Coreia, Sul da Coreia do Sul <br> Europa do Norte, Europa Ocidental <br> Sul do Reino Unido, Oeste do Reino Unido
**Sistemas operacionais de VM com suporte** | SLES 12 com SP2 ou SP3.
**Versões do HANA com suporte** | SDC no HANA 1. x, MDC no HANA 2. x < = SPS03

### <a name="current-limitations"></a>Limitações atuais

- Você só pode fazer backup SAP HANA bancos de dados em execução em VMs do Azure.
- Só é possível fazer backup de SAP HANA instância em execução em uma única VM do Azure. No momento, não há suporte para várias instâncias do HANA na mesma VM do Azure.
- Você só pode fazer backup de bancos de dados no modo de escala vertical. A escala horizontal, ou seja, uma instância do HANA em várias VMs do Azure não tem suporte no momento para backup.
- Não é possível fazer backup SAP HANA instância com camadas dinâmicas no servidor estendido, ou seja, camadas dinâmicas presentes em outro nó. Isso é essencialmente expandido, o que não tem suporte.
- Não é possível fazer backup SAP HANA instância com camadas dinâmicas habilitadas no mesmo servidor. No momento, não há suporte para camadas dinâmicas.
- Você só pode configurar SAP HANA backup no portal do Azure. O recurso não pode ser configurado com o PowerShell, CLI.
- Você pode fazer backup de logs de banco de dados a cada 15 minutos. Os backups de log só começam a fluir após a conclusão de um backup completo bem-sucedido para o banco de dados.
- Você pode fazer backups completos e diferenciais. Atualmente, não há suporte para backup incremental.
- Você não pode modificar a política de backup depois de aplicá-la para backups de SAP HANA. Se você quiser fazer backup com configurações diferentes, crie uma nova política ou atribua uma política diferente.
  - Para criar uma nova política, no cofre, clique em **políticas** > **políticas** > de backup **+ Adicionar** > **SAP Hana na VM do Azure**e especifique as configurações de política.
  - Para atribuir uma política diferente, nas propriedades da VM que executa o banco de dados, clique no nome da política atual. Em seguida, na página **política de backup** , você pode selecionar uma política diferente a ser usada para o backup.

## <a name="prerequisites"></a>Pré-requisitos

Certifique-se de fazer o seguinte antes de configurar os backups:

1. Na VM que executa o banco de dados SAP HANA, instale e habilite pacotes de driver ODBC do pacote/mídia oficial do SLES usando zypper, da seguinte maneira:

    ```unix
    sudo zypper update
    sudo zypper install unixODBC
    ```

2. Permita a conectividade da VM com a Internet, para que ela possa acessar o Azure, conforme descrito no procedimento [abaixo](#set-up-network-connectivity).

3. Execute o script de pré-registro na máquina virtual em que o HANA está instalado como um usuário raiz. O script é fornecido [no portal](#discover-the-databases) no fluxo e é necessário para configurar as [permissões corretas](backup-azure-sap-hana-database-troubleshoot.md#setting-up-permissions).

### <a name="set-up-network-connectivity"></a>Configurar a conectividade de rede

Para todas as operações, a VM SAP HANA precisa de conectividade com os endereços IP públicos do Azure. As operações de VM (descoberta de banco de dados, configurar backups, agendar backups, restaurar pontos de recuperação e assim por diante) não funcionam sem conectividade. Estabeleça a conectividade permitindo o acesso aos intervalos de IP do datacenter do Azure: 

- Você pode baixar os [intervalos de endereços IP](https://www.microsoft.com/download/details.aspx?id=41653) para data centers do Azure e, em seguida, permitir o acesso a esses endereços IP.
- Se você estiver usando NSGs (grupos de segurança de rede), poderá usar a [marca de serviço](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) AzureCloud para permitir todos os endereços IP públicos do Azure. Você pode usar o [cmdlet Set-AzureNetworkSecurityRule](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurenetworksecurityrule?view=azuresmps-4.0.0) para modificar as regras de NSG.
- 443 a porta deve estar na lista de permissões, pois o transporte é via HTTPS.

## <a name="onboard-to-the-public-preview"></a>Integração à visualização pública

Integre à visualização pública da seguinte maneira:

- No portal, registre sua ID de assinatura para o provedor de serviço de serviços de recuperação [seguindo este artigo](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-register-provider-errors#solution-3---azure-portal). 
- Para o PowerShell, execute este cmdlet. Ele deve ser concluído como "registrado".

    ```powershell
    PS C:>  Register-AzProviderFeature -FeatureName "HanaBackup" –ProviderNamespace Microsoft.RecoveryServices
    ```

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-the-databases"></a>Descobrir os bancos de dados

1. No cofre, em **introdução**, clique em **backup**. Em **onde sua carga de trabalho está em execução?** , selecione **SAP Hana na VM do Azure**.
2. Clique em **Iniciar descoberta**. Isso inicia a descoberta de VMs do Linux desprotegidas na região do cofre.

   - Após a descoberta, as VMs desprotegidas aparecem no portal, listadas por nome e grupo de recursos.
   - Se uma VM não estiver listada como esperado, verifique se ela já foi submetida a backup em um cofre.
   - Várias VMs podem ter o mesmo nome, mas pertencem a grupos de recursos diferentes.

3. Em **selecionar máquinas virtuais**, clique no link para baixar o script que fornece permissões para o serviço de backup do Azure acessar as VMs de SAP Hana para descoberta de banco de dados
4. Execute o script em cada VM que hospeda SAP HANA bancos de dados dos quais você deseja fazer backup.
5. Depois de executar o script nas VMs, em **selecionar máquinas virtuais**, selecione as VMs. Em seguida, clique em **descobrir bancos**de os.
6. O backup do Azure descobre todos os bancos de dados SAP HANA na VM. Durante a descoberta, o backup do Azure registra a VM com o cofre e instala uma extensão na VM. Nenhum agente está instalado no banco de dados.

    ![Descobrir bancos de dados SAP HANA](./media/backup-azure-sap-hana-database/hana-discover.png)

## <a name="configure-backup"></a>Configurar a cópia de segurança  

Agora, habilite o backup.

1. Na etapa 2, clique em **Configurar backup**.
2. Em **selecionar itens para fazer backup**, selecione todos os bancos de dados que você deseja proteger > **OK**.
3. Em **política** > de backup,**escolha política de backup**, crie uma nova política de backup para os bancos de dados, de acordo com as instruções abaixo.
4. Depois de criar a política, no menu **backup** , clique em **habilitar backup**.
5. Acompanhe o progresso da configuração de backup na área de **notificações** do Portal.

### <a name="create-a-backup-policy"></a>Criar uma política de backup

Uma política de backup define quando os backups são feitos e por quanto tempo eles são retidos.

- Uma política é criada no nível do cofre.
- Vários cofres podem usar a mesma política de backup, mas você deve aplicar a política de backup a cada cofre.

Especifique as configurações de política da seguinte maneira:

1. Em **nome da política**, insira um nome para a nova política.
2. Em **política de backup completo**, selecione uma **frequência de backup**, escolha **diária** ou **semanal**.
   - **Diariamente**: Selecione a hora e o fuso horário em que o trabalho de backup começa.
   
       - Você deve executar um backup completo. Você não pode desativar essa opção.
       - Clique em **backup completo** para exibir a política.
       - Não é possível criar backups diferenciais para backups diários completos.
       
   - **Semanal**: Selecione o dia da semana, hora e fuso horário em que o trabalho de backup é executado.
3. Em **período de retenção**, defina as configurações de retenção para o backup completo.
    - Por padrão, todas as opções são selecionadas. Desmarque os limites de período de retenção que você não deseja usar e defina os que você faz.
    - O período de retenção mínimo para qualquer tipo de backup (completo/diferencial/log) é de sete dias.
    - Os pontos de recuperação são marcados para retenção com base em seu período de retenção. Por exemplo, se você selecionar um backup completo diário, apenas um backup completo será disparado todos os dias.
    - O backup de um dia específico é marcado e mantido com base no período de retenção semanal e na configuração.
    - Os intervalos de retenção mensais e anuais se comportam de maneira semelhante.

4. No menu **política de backup completo** , clique em **OK** para aceitar as configurações.
5. Selecione **backup diferencial** para adicionar uma política diferencial.
6. Em **política de backup diferencial**, selecione **habilitar** para abrir os controles de frequência e retenção.
    - No máximo, você pode disparar um backup diferencial por dia.
    - Backups diferenciais podem ser retidos por um máximo de 180 dias. Se precisar de maior retenção, você deverá usar backups completos.

    > [!NOTE]
    > Atualmente, não há suporte para backups incrementais. 

7. Clique em **OK** para salvar a política e retornar ao menu principal da **política de backup** .
8. Selecione **backup de log** para adicionar uma política de backup de log transacional,
    - Em **backup de log**, selecione **habilitar**.
    - Defina a frequência e os controles de retenção.

    > [!NOTE]
    > Os backups de log só começam a fluir depois que um backup completo bem-sucedido é concluído.

9. Clique em **OK** para salvar a política e retornar ao menu principal da **política de backup** .
10. Depois de terminar de definir a política de backup, clique em **OK**.


## <a name="run-an-on-demand-backup"></a>Executar um backup sob demanda

Os backups são executados de acordo com o agendamento da política. Você pode executar um backup sob demanda da seguinte maneira:


1. No menu do cofre, clique em **itens de backup**.
2. Em **itens de backup**, selecione a VM que executa o banco de dados SAP Hana e clique em **fazer backup agora**.
3. Em **fazer backup agora**, use o controle de calendário para selecionar o último dia em que o ponto de recuperação deve ser retido. Em seguida, clique em **OK**.
4. Monitore as notificações do Portal. Você pode monitorar o andamento do trabalho no painel do cofre > **trabalhos de Backup** > em andamento. Dependendo do tamanho do banco de dados, a criação do backup inicial pode demorar um pouco.

## <a name="run-sap-hana-studio-backup-on-a-database-with-azure-backup-enabled"></a>Executar o backup do SAP HANA Studio em um banco de dados com o backup do Azure habilitado

Se você quiser fazer um backup local (usando o HANA Studio) de um banco de dados cujo backup está sendo feito com o backup do Azure, faça o seguinte:

1. Aguarde até que os backups completos ou de log do banco de dados sejam concluídos. Verifique o status no SAP HANA Studio.
2. Desabilite os backups de log e defina o catálogo de backup para o sistema de arquivos para o banco de dados relevante.
3. Para fazer isso, clique duas vezes em**configuração** > do **systemdb** > **selecionar** > **filtro de banco de dados (log)** .
4. Defina **enable_auto_log_backup** como **não**.
5. Defina **log_backup_using_backint** como **false**.
6. Faça um backup completo ad hoc do banco de dados.
7. Aguarde até que o backup completo e o backup do catálogo sejam concluídos.
8. Reverter as configurações anteriores para as do Azure:
    - Defina **enable_auto_log_backup** como **Sim**.
    - Defina **log_backup_using_backint** como **true**.


## <a name="upgrading-protected-10-dbs-to-20"></a>Atualizando bancos de 1,0 protegidos para 2,0

Se você estiver protegendo bancos de SAP HANA 1,0 e quiser atualizar para o 2,0, execute as etapas descritas abaixo.

- Pare proteger com reter dados para o antigo SDC DB.
- Execute novamente o script de pré-registro com os detalhes corretos de (Sid e MDC). 
- Registrar novamente a extensão (backup-> Exibir detalhes-> selecionar a VM do Azure relevante-> registrar novamente). 
- Clique em redescobrir bancos de os para a mesma VM. Isso deve mostrar o novo banco de bancos na etapa 2 com detalhes corretos (SYSTEMDB e DB de locatário, não SDC). 
- Proteja esses novos bancos de dados.

## <a name="next-steps"></a>Passos seguintes

[Saiba mais sobre](backup-azure-sap-hana-database-troubleshoot.md) como solucionar erros comuns ao usar o backup SAP Hana em VMs do Azure.
[Saiba mais sobre como](backup-azure-arm-vms-prepare.md) fazer backup de VMs do Azure.
