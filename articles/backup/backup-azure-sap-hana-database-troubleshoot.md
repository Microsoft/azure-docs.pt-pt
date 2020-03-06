---
title: Problemas sap HANA bases de dados erros de backup
description: Descreve como resolver erros comuns que podem ocorrer quando utiliza o Azure Backup para fazer backup nas bases de dados do SAP HANA.
ms.topic: troubleshooting
ms.date: 11/7/2019
ms.openlocfilehash: 8872cfe87df9b8d0553d777f72fe7102d08dea4d
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78382481"
---
# <a name="troubleshoot-backup-of-sap-hana-databases-on-azure"></a>Backup de resolução de problemas das bases de dados da SAP HANA no Azure

Este artigo fornece informações de resolução de problemas para o backup das bases de dados SAP HANA em máquinas virtuais Azure. Para obter mais informações sobre os cenários de backup do SAP HANA que atualmente apoiamos, consulte o [suporte do Cenário](sap-hana-backup-support-matrix.md#scenario-support).

## <a name="prerequisites-and-permissions"></a>Pré-requisitos e Permissões

Consulte os [pré-requisitos](tutorial-backup-sap-hana-db.md#prerequisites) e [o que o script pré-registo faz](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) secções antes de configurar as cópias de segurança.

## <a name="common-user-errors"></a>Erros comuns do utilizador

### <a name="usererrorhanainternalrolenotpresent"></a>UserErrorHANAInternalRoleNotPresent

| **Mensagem de erro**      | <span style="font-weight:normal">Backup azure não tem privilégios de papel necessários para realizar backup</span>    |
| ---------------------- | ------------------------------------------------------------ |
| **Possíveis causas**    | O papel pode ter sido substituído.                          |
| **Ação recomendada** | Para resolver o problema, execute o script a partir do painel **Discover DB,** ou descarregue-o [aqui](https://aka.ms/scriptforpermsonhana). Em alternativa, adicione a função 'SAP_INTERNAL_HANA_SUPPORT' ao Utilizador de Backup workload (AZUREWLBACKUPHANAUSERUSER). |

### <a name="usererrorinopeninghanaodbcconnection"></a>UserErrorInOpeningHanaOdbcConnection

| Mensagem de Erro      | <span style="font-weight:normal">Falha na ligação ao sistema HANA</span>                        |
| ------------------ | ------------------------------------------------------------ |
| **Possíveis causas**    | A instância SAP HANA pode estar em baixo.<br/>As permissões necessárias para que o backup do Azure interaja com a base de dados HANA não estão definidas. |
| **Ação recomendada** | Verifique se a base de dados SAP HANA está no ar. Se a base de dados estiver em funcionamento, verifique se estão definidas todas as permissões necessárias. Se faltarem alguma das permissões, o [script de pré-registo](https://aka.ms/scriptforpermsonhana) para adicionar as permissões em falta. |

### <a name="usererrorhanainstancenameinvalid"></a>UserErrorHanaInstanceNameInvalid

| Mensagem de Erro      | <span style="font-weight:normal">A instância sap HANA especificada é inválida ou não pode ser encontrada</span>  |
| ------------------ | ------------------------------------------------------------ |
| **Possíveis causas**    | Várias instâncias SAP HANA num único VM Azure não podem ser apoiadas. |
| **Ação recomendada** | Execute o [script de pré-registo](https://aka.ms/scriptforpermsonhana) no caso SAP HANA que deseja fazer o apoio. Se o problema persistir, contacte o suporte da Microsoft. |

### <a name="usererrorhanaunsupportedoperation"></a>UserErrorHanaUnsupportedOperation

| Mensagem de Erro      | <span style="font-weight:normal">A especificada operação SAP HANA não é suportada</span>              |
| ------------------ | ------------------------------------------------------------ |
| **Possíveis causas**    | O backup azure para sAP HANA não suporta backup incremental e ações realizadas em clientes nativos SAP HANA (Studio/ Cockpit/ DBA Cockpit) |
| **Ação recomendada** | Para mais informações, consulte [aqui](https://docs.microsoft.com/azure/backup/sap-hana-backup-support-matrix#scenario-support). |

### <a name="usererrorhanapodoesnotsupportbackuptype"></a>UserErrorHANAPONão Suportao Tipo de Backup

| Mensagem de Erro      | <span style="font-weight:normal">Esta base de dados SAP HANA não suporta o tipo de cópia de segurança solicitada</span>  |
| ------------------ | ------------------------------------------------------------ |
| **Possíveis causas**    | Backup Azure não suporta backup incremental e backup usando instantâneos |
| **Ação recomendada** | Para mais informações, consulte [aqui](https://docs.microsoft.com/azure/backup/sap-hana-backup-support-matrix#scenario-support). |

### <a name="usererrorhanalsnvalidationfailure"></a>Falha de validação do usererrorHANALSN

| Mensagem de Erro      | <span style="font-weight:normal">A corrente de registo de reserva está partida</span>                                    |
| ------------------ | ------------------------------------------------------------ |
| **Possíveis causas**    | O destino de backup de registo pode ter sido atualizado de backint para sistema de ficheiros ou o executável backint pode ter sido alterado |
| **Ação recomendada** | Desencadear um backup completo para resolver o problema                   |

### <a name="usererrorincomaptiblesrctargetsystsemsforrestore"></a>UserErrorIncomaptibleSrcTargetSstsemsForRestore

| Mensagem de Erro      | <span style="font-weight:normal">Os sistemas de origem e alvo para restauro são incompatíveis</span>    |
| ------------------ | ------------------------------------------------------------ |
| **Possíveis causas**    | O sistema-alvo para restauro é incompatível com a fonte |
| **Ação recomendada** | Consulte a Nota SAP [1642148](https://launchpad.support.sap.com/#/notes/1642148) para saber sobre os tipos de restauro suportados hoje |

### <a name="usererrorsdctomdcupgradedetected"></a>UserErrorSDCtoMDCUpgradeDetected

| Mensagem de Erro      | <span style="font-weight:normal">SDC para a tualização MDC detetada</span>                                   |
| ------------------ | ------------------------------------------------------------ |
| **Possíveis causas**    | A instância SAP HANA foi atualizada de SDC para MDC. As cópias de segurança falharão após a atualização. |
| **Ação recomendada** | Siga os passos listados na [secção Deupgrade da Secção SAP HANA 1.0 a 2.0](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot#upgrading-from-sap-hana-10-to-20) para resolver o problema |

### <a name="usererrorinvalidbackintconfiguration"></a>UserErrorInvalidBackintConfiguration

| Mensagem de Erro      | <span style="font-weight:normal">Configuração inválida de backint detetada</span>                       |
| ------------------ | ------------------------------------------------------------ |
| **Possíveis causas**    | Os parâmetros de apoio são incorretamente especificados para a cópia de segurança do Azure |
| **Ação recomendada** | Verifique se estão definidos os seguintes parâmetros (backint):<br/>\* [catalog_backup_using_backint:verdade]<br/>\* [enable_accumulated_catalog_backup:falso]<br/>\* [parallel_data_backup_backint_channels:1]<br/>\* [log_backup_timeout_s:900)]<br/>\* [backint_response_timeout:7200]<br/>Se estiverem presentes parâmetros baseados em backint no HOST, retire-os. Se os parâmetros não estiverem presentes ao nível do HOST, mas tiverem sido modificados manualmente a nível da base de dados, reverta-os para os valores apropriados descritos anteriormente. Ou executar [a proteção stop e reter dados](https://docs.microsoft.com/azure/backup/sap-hana-db-manage#stop-protection-for-an-sap-hana-database) de backup do portal Azure e, em seguida, selecionar a cópia de segurança do **Currículo**. |

## <a name="restore-checks"></a>Restaurar os controlos

### <a name="single-container-database-sdc-restore"></a>Restauto da base de dados de contentores único (SDC)

Cuide das inputs enquanto restaura uma única base de dados de contentores (SDC) para HANA para outra máquina SDC. O nome da base de dados deve ser dado com minúscula sueste e com "sdc" anexado em parênteses. A instância HANA será exibida em maiúsculas.

Assuma que um sdc HANA instância "H21" está apoiado. A página de itens de backup mostrará o nome de cópia de segurança como **"h21(sdc)".** Se tentar restaurar esta base de dados para outro alvo SDC, por exemplo H11, então é necessário fornecer inputs.

![Nome de base de dados SDC restaurado](media/backup-azure-sap-hana-database/hana-sdc-restore.png)

Tenha em atenção os seguintes pontos:

- Por predefinição, o nome db restaurado será preenchido com o nome do item de reserva. Neste caso, h21(sdc).
- Selecionando o alvo como H11 NÃO alterará automaticamente o nome db restaurado. **Deve ser editado para h11(sdc)** . No que diz respeito à SDC, o nome db restaurado será o ID de instância-alvo com letras minúsculas e 'sdc' anexado em parênteses.
- Uma vez que o SDC só pode ter uma única base de dados, também precisa de clicar na caixa de verificação para permitir a sobreposição dos dados de base de dados existentes com os dados do ponto de recuperação.
- Linux é sensível a casos. Então tenha cuidado para preservar o caso.

### <a name="multiple-container-database-mdc-restore"></a>Restauro da Base de Dados de Contentores Múltiplos (MDC)

Em várias bases de dados de contentores para HANA, a configuração padrão é SYSTEMDB + 1 ou mais DBs de inquilino. Restaurar uma instância inteira do SAP HANA significa restaurar tanto o SYSTEMDB como o Tenant DBs. Um restaura o SYSTEMDB primeiro e depois procede para o Tenant DB. O SISTEMA DB significa essencialmente anular a informação do sistema sobre o alvo selecionado. Este restauro também substitui as informações relacionadas com backInt na instância alvo. Assim, depois de o sistema DB ser restaurado para uma instância-alvo, executar novamente o script de pré-registo. Só então o inquilino subsequente db restaurados será bem sucedido.

## <a name="upgrading-from-sap-hana-10-to-20"></a>Upgrade de SAP HANA 1.0 para 2.0

Se estiver a proteger as bases de dados SAP HANA 1.0 e pretender fazer o upgrade para 2.0, então execute os seguintes passos:

- [Pare a proteção](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) com dados de retenção para base de dados SDC antiga.
- Faça a atualização. Após a conclusão, o HANA é agora MDC com um sistema DB e inquilino DB(s)
- Reexecutar o [script de pré-inscrição](https://aka.ms/scriptforpermsonhana) com detalhes corretos de (sid e mdc).
- Re-registe a extensão para a mesma máquina no portal Azure (Backup &> ver detalhes -> Selecione o Azure VM -> Re-register) relevante.
- Clique em Redescobrir DBs para o mesmo VM. Esta ação deve mostrar os novos DBs no passo 2 com detalhes corretos (SYSTEMDB e Tenant DB, não SDC).
- Configure cópias de segurança para estas novas bases de dados.

## <a name="upgrading-without-an-sid-change"></a>Upgrade sem uma mudança de SID

As atualizações para OS ou SAP HANA que não causam uma alteração SID podem ser tratadas como descrito abaixo:

- [Parar a proteção](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) com dados de retenção para a base de dados
- Faça a atualização.
- Reexecutar o [script de pré-inscrição](https://aka.ms/scriptforpermsonhana). Normalmente, temos visto o processo de atualização remover as funções necessárias. A execução do script de pré-registo ajudará a verificar todas as funções necessárias.
- Retomar a [proteção](sap-hana-db-manage.md#resume-protection-for-an-sap-hana-database) da base de dados novamente

## <a name="next-steps"></a>Passos seguintes

- Reveja as [perguntas frequentes](https://docs.microsoft.com/azure/backup/sap-hana-faq-backup-azure-vm) sobre o backup das bases de dados SAP HANA em VMs Azure]
