---
title: Solucionar erros de backup de bancos de dados SAP HANA
description: Descreve como solucionar erros comuns que podem ocorrer quando você usa o backup do Azure para fazer backup de bancos de dados do SAP HANA.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: e8bb1d3328f95b647a788c53afe3ac1455eefa13
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2019
ms.locfileid: "74665343"
---
# <a name="troubleshoot-backup-of-sap-hana-databases-on-azure"></a>Solucionar problemas de backup de bancos de dados SAP HANA no Azure

Este artigo fornece informações de solução de problemas para fazer backup de bancos de dados SAP HANA em máquinas virtuais do Azure. Para obter mais informações sobre os cenários de backup SAP HANA que atualmente damos suporte, consulte [suporte a cenários](sap-hana-backup-support-matrix.md#scenario-support).

## <a name="prerequisites-and-permissions"></a>Pré-requisitos e permissões

Consulte as seções [pré-requisitos](tutorial-backup-sap-hana-db.md#prerequisites) e [Configurando permissões](tutorial-backup-sap-hana-db.md#setting-up-permissions) antes de configurar backups.

## <a name="common-user-errors"></a>Erros comuns do usuário

### <a name="usererrorinopeninghanaodbcconnection"></a>UserErrorInOpeningHanaOdbcConnection

| Mensagem de Erro      | <span style="font-weight:normal">Falha ao conectar ao sistema HANA</span>                        |
| ------------------ | ------------------------------------------------------------ |
| **Possíveis causas**    | A instância de SAP HANA pode estar inativa.<br/>As permissões necessárias para o backup do Azure interagir com o banco de dados HANA não estão definidas. |
| **Ação recomendada** | Verifique se o banco de dados do SAP HANA está ativo. Se o banco de dados estiver em execução, verifique se todas as permissões necessárias estão definidas. Se alguma das permissões estiver ausente, execute o [script de auto-registro](https://aka.ms/scriptforpermsonhana) para adicionar as permissões ausentes. |

### <a name="usererrorhanainstancenameinvalid"></a>UserErrorHanaInstanceNameInvalid

| Mensagem de Erro      | <span style="font-weight:normal">A instância de SAP HANA especificada é inválida ou não pode ser encontrada</span>  |
| ------------------ | ------------------------------------------------------------ |
| **Possíveis causas**    | Não é possível fazer backup de várias instâncias do SAP HANA em uma única VM do Azure. |
| **Ação recomendada** | Execute o [script de auto-registro](https://aka.ms/scriptforpermsonhana) na instância de SAP Hana que você deseja fazer backup. Se o problema ainda persistir, entre em contato com o suporte da Microsoft. |

### <a name="usererrorhanaunsupportedoperation"></a>UserErrorHanaUnsupportedOperation

| Mensagem de Erro      | <span style="font-weight:normal">Não há suporte para a operação de SAP HANA especificada</span>              |
| ------------------ | ------------------------------------------------------------ |
| **Possíveis causas**    | O backup do Azure para SAP HANA não dá suporte a backup incremental e ações executadas em clientes SAP HANA nativos (ferramenta cockpit de Studio/cockpit/DBA) |
| **Ação recomendada** | Para obter mais informações, consulte [aqui](https://docs.microsoft.com/azure/backup/sap-hana-backup-support-matrix#scenario-support). |

### <a name="usererrorhanapodoesnotsupportbackuptype"></a>UserErrorHANAPODoesNotSupportBackupType

| Mensagem de Erro      | <span style="font-weight:normal">Este banco de dados SAP HANA não dá suporte ao tipo de backup solicitado</span>  |
| ------------------ | ------------------------------------------------------------ |
| **Possíveis causas**    | O backup do Azure não dá suporte a backup incremental e backup usando instantâneos |
| **Ação recomendada** | Para obter mais informações, consulte [aqui](https://docs.microsoft.com/azure/backup/sap-hana-backup-support-matrix#scenario-support). |

### <a name="usererrorhanalsnvalidationfailure"></a>UserErrorHANALSNValidationFailure

| Mensagem de Erro      | <span style="font-weight:normal">A cadeia de logs de backup está quebrada</span>                                    |
| ------------------ | ------------------------------------------------------------ |
| **Possíveis causas**    | O destino do backup de log pode ter sido atualizado de BACKINT para o sistema de arquivos ou o executável BACKINT pode ter sido alterado |
| **Ação recomendada** | Disparar um backup completo para resolver o problema                   |

### <a name="usererrorincomaptiblesrctargetsystsemsforrestore"></a>UserErrorIncomaptibleSrcTargetSystsemsForRestore

| Mensagem de Erro      | <span style="font-weight:normal">Os sistemas de origem e de destino para restauração são incompatíveis</span>    |
| ------------------ | ------------------------------------------------------------ |
| **Possíveis causas**    | O sistema de destino para restauração é incompatível com a origem |
| **Ação recomendada** | Consulte a observação do SAP [1642148](https://launchpad.support.sap.com/#/notes/1642148) para saber mais sobre os tipos de restauração com suporte hoje |

### <a name="usererrorsdctomdcupgradedetected"></a>UserErrorSDCtoMDCUpgradeDetected

| Mensagem de Erro      | <span style="font-weight:normal">SDC para a atualização MDC detectada</span>                                   |
| ------------------ | ------------------------------------------------------------ |
| **Possíveis causas**    | A instância de SAP HANA foi atualizada de SDC para MDC. Os backups falharão após a atualização. |
| **Ação recomendada** | Siga as etapas listadas na [seção Atualizando do SAP HANA 1,0 para 2,0](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot#upgrading-from-sap-hana-10-to-20) para resolver o problema |

### <a name="usererrorinvalidbackintconfiguration"></a>UserErrorInvalidBackintConfiguration

| Mensagem de Erro      | <span style="font-weight:normal">Configuração de BACKINT inválida detectada</span>                       |
| ------------------ | ------------------------------------------------------------ |
| **Possíveis causas**    | Os parâmetros de backup estão especificados incorretamente para o backup do Azure |
| **Ação recomendada** | Verifique se os seguintes parâmetros (BACKINT) estão definidos:<br/>\* [catalog_backup_using_backint: true]<br/>\* [enable_accumulated_catalog_backup: false]<br/>\* [parallel_data_backup_backint_channels: 1]<br/>\* [log_backup_timeout_s: 900)]<br/>\* [backint_response_timeout: 7200]<br/>Se os parâmetros baseados em BACKINT estiverem presentes no HOST, remova-os. Se os parâmetros não estiverem presentes no nível do HOST, mas tiverem sido modificados manualmente em um nível de banco de dados, reverta-os para os valores apropriados, conforme descrito anteriormente. Ou execute o [Stop Protection e mantenha os dados de backup](https://docs.microsoft.com/azure/backup/sap-hana-db-manage#stop-protection-for-an-sap-hana-database) do portal do Azure e selecione **retomar backup**. |

## <a name="restore-checks"></a>Restaurar verificações

### <a name="single-container-database-sdc-restore"></a>Restauração de banco de dados de contêiner único (SDC)

Tome cuidado com as entradas durante a restauração de um banco de dados de contêiner único (SDC) para HANA para outro computador SDC. O nome do banco de dados deve ser fornecido com letras minúsculas e com "SDC" anexado entre colchetes. A instância do HANA será exibida em maiúsculas.

Suponha que uma instância do HANA do SDC "H21" seja submetida a backup. A página itens de backup mostrará o nome do item de backup como **"H21 (SDC)"** . Se você tentar restaurar esse banco de dados para outro SDC de destino, digamos H11, as entradas a seguir precisarão ser fornecidas.

![SDC restaurar entradas](media/backup-azure-sap-hana-database/hana-sdc-restore.png)

Tenha em atenção os seguintes pontos:

- Por padrão, o nome do BD restaurado será populado com o nome do item de backup, ou seja, H21 (SDC)
- Selecionar o destino como H11 não alterará automaticamente o nome do banco de BD restaurado. **Ele deve ser editado para H11 (SDC)** . Em relação a SDC, o nome do BD restaurado será a ID da instância de destino com letras minúsculas e ' SDC ' anexado entre colchetes.
- Como o SDC pode ter apenas um único banco de dados, você também precisa clicar na caixa de seleção para permitir a substituição dos existentes nos data com os dados do ponto de recuperação.
- O Linux diferencia maiúsculas de minúsculas. Portanto, tenha cuidado para preservar o caso.

### <a name="multiple-container-database-mdc-restore"></a>Restauração de banco de dados de contêiner múltiplo (MDC)

Em vários bancos de dados de contêiner para HANA, a configuração padrão é SYSTEMDB + 1 ou mais bancos de dados de locatário. A restauração de uma instância de SAP HANA inteira significa restaurar bancos de SYSTEMDB e locatários. Uma restaura SYSTEMDB primeiro e, em seguida, prossegue para o banco de do locatário. Essencialmente, o banco de dados do sistema significa substituir as informações do sistema no destino selecionado. Essa restauração também substitui as informações relacionadas ao BackInt na instância de destino. Portanto, depois que o banco de BD do sistema for restaurado para uma instância de destino, será necessário executar o script de pré-registro novamente. Somente as restaurações subsequentes do banco de bancos de locatário serão realizadas com sucesso.

## <a name="upgrading-from-sap-hana-10-to-20"></a>Atualizando do SAP HANA 1,0 para 2,0

Se você estiver protegendo bancos de dados SAP HANA 1,0 e quiser atualizar para o 2,0, execute as etapas descritas abaixo:

- [Pare a proteção](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) com reter dados para o antigo banco de SDC.
- Execute o [script de pré-registro](https://aka.ms/scriptforpermsonhana) novamente com os detalhes corretos de (Sid e MDC).
- Registrar novamente a extensão (backup-> Exibir detalhes-> selecionar a VM do Azure relevante-> registrar novamente).
- Clique em redescobrir bancos de os para a mesma VM. Essa ação deve mostrar o novo banco de bancos na etapa 2 com detalhes corretos (SYSTEMDB e DB de locatário, não SDC).
- Proteja esses novos bancos de dados.

## <a name="upgrading-without-an-sid-change"></a>Atualizando sem uma alteração de SID

As atualizações para o sistema operacional ou SAP HANA que não causam uma alteração de SID podem ser manipuladas conforme descrito abaixo:

- [Interromper a proteção](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) com reter dados para o Database
- Executar novamente o [script de pré-registro](https://aka.ms/scriptforpermsonhana)
- [Retomar a proteção](sap-hana-db-manage.md#resume-protection-for-an-sap-hana-database) para o banco de dados novamente

## <a name="next-steps"></a>Passos seguintes

- Examine as [perguntas](https://docs.microsoft.com/azure/backup/sap-hana-faq-backup-azure-vm) frequentes sobre como fazer backup de bancos de dados SAP Hana em VMs do Azure]
