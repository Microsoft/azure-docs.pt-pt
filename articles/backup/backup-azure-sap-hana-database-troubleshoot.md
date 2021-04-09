---
title: Resolução de problemas SAP HANA erros de backup
description: Descreve como resolver erros comuns que podem ocorrer quando utiliza o Azure Backup para fazer backup nas bases de dados SAP HANA.
ms.topic: troubleshooting
ms.date: 11/7/2019
ms.openlocfilehash: 22800adc323bda8a60278160f24bc559103fb57e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101713342"
---
# <a name="troubleshoot-backup-of-sap-hana-databases-on-azure"></a>Backup de resolução de problemas das bases de dados SAP HANA em Azure

Este artigo fornece informações sobre resolução de problemas para o backup das bases de dados SAP HANA em máquinas virtuais Azure. Para obter mais informações sobre os cenários de backup SAP HANA que apoiamos atualmente, consulte [o suporte do Scenario](sap-hana-backup-support-matrix.md#scenario-support).

## <a name="prerequisites-and-permissions"></a>Pré-requisitos e permissões

Consulte os [pré-requisitos](tutorial-backup-sap-hana-db.md#prerequisites) e [o que o script de pré-registo faz](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) antes de configurar cópias de segurança.

## <a name="common-user-errors"></a>Erros comuns do utilizador

### <a name="usererrorhanainternalrolenotpresent"></a>UserErrorHANAInternalRoleNotPresent

| **Mensagem de Erro**      | <span style="font-weight:normal">Azure Backup não tem privilégios de papel necessários para realizar backup</span>    |
| ---------------------- | ------------------------------------------------------------ |
| **Possíveis causas**    | O papel pode ter sido substituído.                          |
| **Ação recomendada** | Para resolver o problema, execute o script a partir do painel **Discover DB** ou descarregue-o [aqui](https://aka.ms/scriptforpermsonhana). Em alternativa, adicione o papel de "SAP_INTERNAL_HANA_SUPPORT" ao utilizador de backup da carga de trabalho (AZUREWLBACKUPHANAUSER). |

### <a name="usererrorinopeninghanaodbcconnection"></a>UserErrorInOpeningHanaOdbcConnection

| Mensagem de Erro      | <span style="font-weight:normal">Falhou em ligar-se ao sistema HANA</span>                        |
| ------------------ | ------------------------------------------------------------ |
| **Possíveis causas**    | O caso SAP HANA pode estar em baixo.<br/>As permissões necessárias para que o Azure Backup interaja com a base de dados HANA não estão definidas. |
| **Ação recomendada** | Verifique se a base de dados SAP HANA está em cima. Se a base de dados estiver em funcionamento, verifique se estão definidas todas as permissões necessárias. Se faltar alguma das permissões, executar o [script de pré-registo](https://aka.ms/scriptforpermsonhana) para adicionar as permissões em falta. |

### <a name="usererrorhanainstancenameinvalid"></a>UserErrorHanaInstanceNameInvalid

| Mensagem de Erro      | <span style="font-weight:normal">A instância SAP HANA especificada é inválida ou não pode ser encontrada</span>  |
| ------------------ | ------------------------------------------------------------ |
| **Possíveis causas**    | Vários casos de SAP HANA num único Azure VM não podem ser apoiados. |
| **Ação recomendada** | Execute o [script de pré-registo](https://aka.ms/scriptforpermsonhana) no caso SAP HANA que pretende fazer recuar. Se o problema ainda persistir, contacte o suporte da Microsoft. |

### <a name="usererrorhanaunsupportedoperation"></a>UserErrorHanaUnsupportedOperation

| Mensagem de Erro      | <span style="font-weight:normal">A operação SAP HANA especificada não é suportada</span>              |
| ------------------ | ------------------------------------------------------------ |
| **Possíveis causas**    | A Azure Backup para SAP HANA não suporta backup incremental e ações realizadas em clientes nativos SAP HANA (Studio/ Cockpit/ DBA Cockpit) |
| **Ação recomendada** | Para mais informações, consulte [aqui.](./sap-hana-backup-support-matrix.md#scenario-support) |

### <a name="usererrorhanalsnvalidationfailure"></a>UserErrorHANALSNValidationFailure

| Mensagem de Erro      | <span style="font-weight:normal">A corrente de registo de backup está quebrada</span>                                    |
| ------------------ | ------------------------------------------------------------ |
| **Possíveis causas**    | O destino de backup de registo pode ter sido atualizado de backint para sistema de ficheiros ou o backint executável pode ter sido alterado |
| **Ação recomendada** | Desencadeie uma cópia de segurança completa para resolver o problema                   |

### <a name="usererrorsdctomdcupgradedetected"></a>UserErrSDCtoMDCUpgradado

| Mensagem de Erro      | <span style="font-weight:normal">SDC para atualização de MDC detetada</span>                                   |
| ------------------ | ------------------------------------------------------------ |
| **Possíveis causas**    | O caso SAP HANA foi atualizado de SDC para MDC. As cópias de segurança falharão após a atualização. |
| **Ação recomendada** | Siga os passos listados no [SDC para atualização do MDC](#sdc-to-mdc-upgrade-with-a-change-in-sid) para resolver o problema |

### <a name="usererrorinvalidbackintconfiguration"></a>UserErrorInvalidBackintConfiguration

| Mensagem de Erro      | <span style="font-weight:normal">Configuração inválida de retroint inválida detetada</span>                       |
| ------------------ | ------------------------------------------------------------ |
| **Possíveis causas**    | Os parâmetros de suporte são especificados incorretamente para Azure Backup |
| **Ação recomendada** | Verifique se estão definidos os seguintes parâmetros (backint):<br/>\* [catalog_backup_using_backint:verdade]<br/>\* [enable_accumulated_catalog_backup:falso]<br/>\* [parallel_data_backup_backint_channels:1]<br/>\* [log_backup_timeout_s:900)]<br/>\* [backint_response_timeout:7200]<br/>Se os parâmetros baseados em backint estiverem presentes no HOST, remova-os. Se os parâmetros não estiverem presentes ao nível do HOST, mas tiverem sido modificados manualmente a um nível de base de dados, reverta-os para os valores apropriados, tal como descrito anteriormente. Ou, [executar a proteção stop e reter dados](./sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) de backup do portal Azure e, em seguida, selecione **Retomar a cópia de segurança**. |

### <a name="usererrorincompatiblesrctargetsystemsforrestore"></a>UserErrorIncompatávelSrcTargetSystemsForRestore

|Mensagem de Erro  |Os sistemas de origem e alvo para a restauração são incompatíveis  |
|---------|---------|
|Possíveis causas   | Os sistemas de origem e alvo selecionados para restauro são incompatíveis        |
|Ação recomendada   |   Certifique-se de que o seu cenário de restauro não está na seguinte lista de possíveis restauros incompatíveis: <br><br>   **Caso 1:** O SYSTEMDB não pode ser renomeado durante a restauração.  <br><br> **Caso 2:** Fonte - SDC e target - MDC: A base de dados de origem não pode ser restaurada como SYSTEMDB ou inquilino DB no alvo. <br><br> **Caso 3:** Fonte - MDC e alvo - SDC: A base de dados de origem (SYSTEMDB ou DB inquilino) não pode ser restaurada ao alvo. <br><br>  Para mais informações, consulte a nota **1642148** na [plataforma de lançamento de suporte SAP](https://launchpad.support.sap.com). |

## <a name="restore-checks"></a>Restaurar verificações

### <a name="single-container-database-sdc-restore"></a>Restauração da Base de Dados de Contentores Únicos (SDC)

Cuide das entradas enquanto restaura uma única base de dados de contentores (SDC) para HANA para outra máquina SDC. O nome da base de dados deve ser dado com minúsculas e com "sdc" anexado em parênteses. A instância HANA será exibida nas capitais.

Assuma que um exemplo de SDC HANA "H21" está apoiado. A página de itens de cópia de segurança mostrará o nome do item de reserva como **"h21(sdc)"**. Se tentar restaurar esta base de dados para outro SDC alvo, digamos H11, então as entradas devem ser fornecidas.

![Nome da base de dados SDC restaurado](media/backup-azure-sap-hana-database/hana-sdc-restore.png)

Tenha em atenção os seguintes pontos:

- Por predefinição, o nome db restaurado será preenchido com o nome do item de reserva. Neste caso, h21(sdc).
- Selecionar o alvo como H11 não alterará automaticamente o nome db restaurado. **Deve ser editado para h11(sdc)**. No que diz respeito ao SDC, o nome db restaurado será o ID de instância-alvo com letras minúsculas e 'sdc' anexados em parênteses.
- Uma vez que o SDC pode ter apenas uma única base de dados, também precisa de selecionar a caixa de verificação para permitir a substituição dos dados da base de dados existentes com os dados do ponto de recuperação.
- Linux é sensível a casos. Então tenha cuidado para preservar o caso.

### <a name="multiple-container-database-mdc-restore"></a>Restauro da Base de Dados de Vários Contentores (MDC)

Em várias bases de dados de contentores para HANA, a configuração padrão é SYSTEMDB + 1 ou mais DBs do Inquilino. Restaurar uma instância SAP HANA significa restaurar tanto o SYSTEMDB como o Tenant DBs. Um restaura systemdb primeiro e, em seguida, procede para O Inquilino DB. O DB do sistema significa essencialmente anular as informações do sistema no alvo selecionado. Esta restauração também substitui as informações relacionadas com o BackInt na instância-alvo. Assim, depois de o DB do sistema ser restaurado para uma instância-alvo, executar novamente o script de pré-registo. Só então o inquilino subsequente DB restaura será bem sucedido.

## <a name="back-up-a-replicated-vm"></a>Apoiar um VM replicado

### <a name="scenario-1"></a>Cenário 1

O VM original foi replicado usando a recuperação do local de Azure ou a cópia de segurança Azure VM. O novo VM foi construído para simular o antigo VM. Ou seja, as configurações são exatamente as mesmas. (Isto porque o VM original foi eliminado e a restauração foi feita a partir de backup VM ou Azure Site Recovery).

Este cenário pode incluir dois casos possíveis. Saiba como apoiar o VM replicado em ambos os casos:

1. O novo VM criado tem o mesmo nome, e está no mesmo grupo de recursos e subscrição que o VM eliminado.

    - A extensão já está presente no VM, mas não é visível para nenhum dos serviços
    - Executar o script de pré-registo
    - Re-registrar a extensão para a mesma máquina no portal Azure **(Backup**  ->  **Ver detalhes** -> Selecione o Azure VM -> re-registro)
    - As bases de dados já existentes (a partir do VM eliminado) devem então começar a ser apoiadas com sucesso

2. O novo VM criado tem:

    - um nome diferente do VM eliminado
    - o mesmo nome que o VM eliminado, mas está num grupo de recursos ou subscrição diferente (em comparação com o VM eliminado)

    Se for esse o caso, então faça os seguintes passos:

    - A extensão já está presente no VM, mas não é visível para nenhum dos serviços
    - Executar o script de pré-registo
    - Se descobrir e proteger as novas bases de dados, começará a ver bases de dados ativas duplicadas no portal. Para evitar isto, [pare a proteção com os dados de retenção](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) para as bases de dados antigas. Em seguida, continue com os passos restantes.
    - Descubra as bases de dados para permitir o backup
    - Ativar cópias de segurança nestas bases de dados
    - As bases de dados já existentes (do VM eliminado) continuarão a ser armazenadas no cofre (mantendo-se as suas cópias de segurança de acordo com a política)

### <a name="scenario-2"></a>Cenário 2

O VM original foi replicado usando a recuperação do local de Azure ou a cópia de segurança Azure VM. O novo VM foi construído a partir do conteúdo – para ser usado como modelo. Este é um novo VM com um novo SID.

Siga estes passos para permitir cópias de segurança no novo VM:

- A extensão já está presente no VM, mas não visível para nenhum dos serviços
- Executar o roteiro de pré-registo. Com base no SID do novo VM, podem surgir dois cenários:
  - O VM original e o novo VM têm o mesmo SID. O script de pré-registo será executado com sucesso.
  - O VM original e o novo VM têm DIFERENTES SIDs. O script de pré-registo falhará. Contacte o suporte para obter ajuda neste cenário.
- Descubra as bases de dados que pretende fazer
- Ativar cópias de segurança nestas bases de dados

## <a name="sdc-version-upgrade-or-mdc-version-upgrade-on-the-same-vm"></a>Upgrade da versão SDC ou atualização da versão MDC no mesmo VM

As atualizações para o SISTEMA, alteração da versão SDC ou alteração da versão MDC que não causem uma alteração sid podem ser manuseadas da seguinte forma:

- Certifique-se de que a nova versão SO, SDC ou MDC são atualmente [suportadas pela Azure Backup](sap-hana-backup-support-matrix.md#scenario-support)
- [Parar a proteção com os dados de retenção](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) para a base de dados
- Realizar a atualização ou atualização
- Reencaúndio do script de pré-registo. Frequentemente, o processo de atualização pode remover [as funções necessárias](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does). Executar o script de pré-registo ajudará a verificar todas as funções necessárias.
- Retomar a proteção da base de dados novamente

## <a name="sdc-to-mdc-upgrade-with-no-change-in-sid"></a>SDC para atualização de MDC sem alterações no SID

As atualizações de SDC para MDC que não causam uma alteração sid podem ser manuseadas da seguinte forma:

- Certifique-se de que a nova versão MDC é atualmente [suportada pelo Azure Backup](sap-hana-backup-support-matrix.md#scenario-support)
- [Parar a proteção com os dados de retenção](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) para a antiga base de dados do SDC
- Execute a atualização. Após a conclusão, o sistema HANA é agora MDC com um sistema DB e DBs inquilinos
- Reexecutar o [script de pré-registo](https://aka.ms/scriptforpermsonhana)
- Re-registrar a extensão para a mesma máquina no portal Azure **(Backup**  ->  **Ver detalhes** -> Selecione o Azure VM -> re-registro)
- Selecione **Redescobrir DBs** para o mesmo VM. Esta ação deve mostrar os novos DBs no passo 3 como SYSTEMDB e Tenant DB, e não SDC
- A base de dados mais antiga da SDC continuará a existir no cofre e terá dados antigos de apoio mantidos de acordo com a política
- Configurar backup para estas bases de dados

## <a name="sdc-to-mdc-upgrade-with-a-change-in-sid"></a>SDC para MDC upgrade com uma mudança no SID

As atualizações de SDC para MDC que causam uma alteração sid podem ser manuseadas da seguinte forma:

- Certifique-se de que a nova versão MDC é atualmente [suportada pelo Azure Backup](sap-hana-backup-support-matrix.md#scenario-support)
- **Parar a proteção com os dados de retenção** para a antiga base de dados do SDC
- Execute a atualização. Após a conclusão, o sistema HANA é agora MDC com um sistema DB e DBs inquilinos
- Reencamúndio do [script de pré-registo](https://aka.ms/scriptforpermsonhana) com detalhes corretos (novos SID e MDC). Devido a uma mudança no SID, poderá enfrentar problemas com a execução com sucesso do script. Contacte o suporte de backup da Azure se tiver problemas.
- Re-registrar a extensão para a mesma máquina no portal Azure **(Backup**  ->  **Ver detalhes** -> Selecione o Azure VM -> re-registro)
- Selecione **Redescobrir DBs** para o mesmo VM. Esta ação deve mostrar os novos DBs no passo 3 como SYSTEMDB e Tenant DB, e não SDC
- A base de dados mais antiga da SDC continuará a existir no cofre e terá dados antigos retidos de acordo com a política
- Configurar backup para estas bases de dados

## <a name="re-registration-failures"></a>Falhas de re-registo

Verifique se existem um ou mais dos seguintes sintomas antes de desencadear a operação de re-registo:

- Todas as operações (como cópia de segurança, restauro e cópia de segurança de configuração) estão a falhar no VM com um dos seguintes códigos de erro: **WorkloadExtensionNotReachable, UserErrorWorkloadExtensionNotInstalled, WorkloadExtensionNotPresent, WorkloadExtensionDididntDequeueMsg**.
- Se a área **de Estado de Backup** do item de backup mostrar Não é **alcançável,** exclua todas as outras causas que possam resultar no mesmo estado:

  - Falta de autorização para realizar operações relacionadas com backup no VM
  - O VM está desligado, por isso os backups não podem ter lugar.
  - Problemas de rede

Estes sintomas podem surgir por uma ou mais das seguintes razões:

- Uma extensão foi eliminada ou desinstalada do portal.
- O VM foi restaurado no tempo através da restauração do disco no local.
- O VM foi desligado por um período prolongado, pelo que a configuração da extensão expirou.
- O VM foi eliminado, e outro VM foi criado com o mesmo nome e no mesmo grupo de recursos que o VM eliminado.

Nos cenários anteriores, recomendamos que desencadeie uma operação de re-registo no VM.

## <a name="next-steps"></a>Passos seguintes

- Reveja as [perguntas frequentes](./sap-hana-faq-backup-azure-vm.md) sobre o backup das bases de dados SAP HANA em VMs Azure.
