---
title: Problemas sap HANA bases de dados erros de backup
description: Descreve como resolver erros comuns que podem ocorrer quando utiliza o Azure Backup para fazer backup nas bases de dados do SAP HANA.
ms.topic: troubleshooting
ms.date: 11/7/2019
ms.openlocfilehash: 5c1ad55a86e80808b9055fd1b34a2d72209464a2
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83697075"
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

### <a name="usererrorsdctomdcupgradedetected"></a>UserErrorSDCtoMDCUpgradeDetected

| Mensagem de Erro      | <span style="font-weight:normal">SDC para a tualização MDC detetada</span>                                   |
| ------------------ | ------------------------------------------------------------ |
| **Possíveis causas**    | A instância SAP HANA foi atualizada de SDC para MDC. As cópias de segurança falharão após a atualização. |
| **Ação recomendada** | Siga os passos listados na [atualização do SDC para a AdC](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot#sdc-to-mdc-upgrade-with-a-change-in-sid) para resolver o problema |

### <a name="usererrorinvalidbackintconfiguration"></a>UserErrorInvalidBackintConfiguration

| Mensagem de Erro      | <span style="font-weight:normal">Configuração inválida de backint detetada</span>                       |
| ------------------ | ------------------------------------------------------------ |
| **Possíveis causas**    | Os parâmetros de apoio são incorretamente especificados para a cópia de segurança do Azure |
| **Ação recomendada** | Verifique se estão definidos os seguintes parâmetros (backint):<br/>\*[catalog_backup_using_backint:verdade]<br/>\*[enable_accumulated_catalog_backup:falso]<br/>\*[parallel_data_backup_backint_channels:1]<br/>\*[log_backup_timeout_s:900)]<br/>\*[backint_response_timeout:7200]<br/>Se estiverem presentes parâmetros baseados em backint no HOST, retire-os. Se os parâmetros não estiverem presentes ao nível do HOST, mas tiverem sido modificados manualmente a nível da base de dados, reverta-os para os valores apropriados descritos anteriormente. Ou executar [a proteção stop e reter dados](https://docs.microsoft.com/azure/backup/sap-hana-db-manage#stop-protection-for-an-sap-hana-database) de backup do portal Azure e, em seguida, selecionar a cópia de segurança do **Currículo**. |

### <a name="usererrorincompatiblesrctargetsystemsforrestore"></a>UserErrorIncompatívelSrcTargetSystemsForRestore

|Mensagem de Erro  |Os sistemas de origem e alvo para restauro são incompatíveis  |
|---------|---------|
|Possíveis causas   | Os sistemas de origem e alvo selecionados para restauro são incompatíveis        |
|Ação recomendada   |   Certifique-se de que o seu cenário de restauro não está na seguinte lista de possíveis restauros incompatíveis: <br><br>   **Caso 1:** SystemDB não pode ser renomeado durante a restauração.  <br><br> **Caso 2:** Fonte - SDC e alvo - MDC: A base de dados de origem não pode ser restaurada como SYSTEMDB ou tenant DB no alvo. <br><br> **Caso 3:** Fonte - MDC e alvo - SDC: A base de dados de origem (SYSTEMDB ou inquilino DB) não pode ser restaurada ao alvo. <br><br>  Para mais informações, consulte a nota **1642148** na plataforma de lançamento de [suporte SAP](https://launchpad.support.sap.com). |

## <a name="restore-checks"></a>Restaurar os controlos

### <a name="single-container-database-sdc-restore"></a>Restauto da base de dados de contentores único (SDC)

Cuide das inputs enquanto restaura uma única base de dados de contentores (SDC) para HANA para outra máquina SDC. O nome da base de dados deve ser dado com minúscula sueste e com "sdc" anexado em parênteses. A instância HANA será exibida em maiúsculas.

Assuma que um sdc HANA instância "H21" está apoiado. A página de itens de backup mostrará o nome de cópia de segurança como **"h21(sdc)".** Se tentar restaurar esta base de dados para outro alvo SDC, por exemplo H11, então é necessário fornecer inputs.

![Nome de base de dados SDC restaurado](media/backup-azure-sap-hana-database/hana-sdc-restore.png)

Tenha em atenção os seguintes pontos:

- Por predefinição, o nome db restaurado será preenchido com o nome do item de reserva. Neste caso, h21(sdc).
- Selecionando o alvo como H11 NÃO alterará automaticamente o nome db restaurado. **Deve ser editado para h11(sdc)**. No que diz respeito à SDC, o nome db restaurado será o ID de instância-alvo com letras minúsculas e 'sdc' anexado em parênteses.
- Uma vez que o SDC só pode ter uma única base de dados, também precisa de clicar na caixa de verificação para permitir a sobreposição dos dados de base de dados existentes com os dados do ponto de recuperação.
- Linux é sensível a casos. Então tenha cuidado para preservar o caso.

### <a name="multiple-container-database-mdc-restore"></a>Restauro da Base de Dados de Contentores Múltiplos (MDC)

Em várias bases de dados de contentores para HANA, a configuração padrão é SYSTEMDB + 1 ou mais DBs de inquilino. Restaurar uma instância inteira do SAP HANA significa restaurar tanto o SYSTEMDB como o Tenant DBs. Um restaura o SYSTEMDB primeiro e depois procede para o Tenant DB. O SISTEMA DB significa essencialmente anular a informação do sistema sobre o alvo selecionado. Este restauro também substitui as informações relacionadas com backInt na instância alvo. Assim, depois de o sistema DB ser restaurado para uma instância-alvo, executar novamente o script de pré-registo. Só então o inquilino subsequente db restaurados será bem sucedido.

## <a name="back-up-a-replicated-vm"></a>Apoiar um VM replicado

### <a name="scenario-1"></a>Cenário 1

O VM original foi replicado usando a recuperação do site Azure ou cópia de segurança Azure VM. O novo VM foi construído para simular o velho VM. Ou seja, as definições são exatamente as mesmas. (Isto porque o VM original foi eliminado e o restauro foi feito a partir de backup VM ou Recuperação do Site Azure).

Este cenário pode incluir dois casos possíveis. Aprenda a apoiar o VM replicado em ambos os casos:

1. O novo VM criado tem o mesmo nome, e está no mesmo grupo de recursos e subscrição que o VM eliminado.

    - A extensão já está presente no VM, mas não é visível para nenhum dos serviços
    - Executar o script de pré-inscrição
    - Re-registe a extensão da mesma máquina no portal Azure (**Backup**  ->  **View detalhes** -> Selecione o registo de VM -> Azure relevante)
    - As bases de dados já existentes (a partir do VM eliminado) devem então começar a ser apoiadas com sucesso

2. O novo VM criado também tem:

    - um nome diferente do VM eliminado
    - o mesmo nome que o VM eliminado, mas está num grupo de recursos ou subscrição diferente (em comparação com o VM eliminado)

    Se for esse o caso, então faça os seguintes passos:

    - A extensão já está presente no VM, mas não é visível para nenhum dos serviços
    - Executar o script de pré-inscrição
    - Se descobrir e proteger as novas bases de dados, começará a ver duplicadas bases de dados ativas no portal. Para evitar isto, [pare a proteção com dados](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) de retenção para as bases de dados antigas. Em seguida, continue com os passos restantes.
    - Descubra as bases de dados para permitir a cópia de segurança
    - Ativar cópias de segurança nestas bases de dados
    - As bases de dados já existentes (a partir do VM eliminado) continuarão a ser armazenadas no cofre (com os seus backups a serem retidos de acordo com a apólice)

### <a name="scenario-2"></a>Cenário 2

O VM original foi replicado usando a recuperação do site Azure ou cópia de segurança Azure VM. O novo VM foi construído a partir do conteúdo – para ser usado como modelo. Este é um novo VM com um novo SID.

Siga estes passos para permitir cópias de segurança no novo VM:

- A extensão já está presente no VM, mas não visível a nenhum dos serviços
- Execute o script de pré-inscrição. Com base no SID do novo VM, podem surgir dois cenários:
  - O VM original e o novo VM têm o mesmo SID. O guião de pré-inscrição será executado com sucesso.
  - O VM original e o novo VM têm Diferentes SIDs. O guião pré-inscrição falhará. Suporte de contato para obter ajuda neste cenário.
- Descubra as bases de dados que pretende apoiar
- Ativar cópias de segurança nestas bases de dados

## <a name="sdc-version-upgrade-or-mdc-version-upgrade-on-the-same-vm"></a>Upgrade de versão SDC ou atualização da versão MDC no mesmo VM

As atualizações para a versão OS, SDC mudam ou a versão MDC alterações que não causam uma alteração SID podem ser tratadas da seguinte forma:

- Certifique-se de que a nova versão OS, SDC ou MDC é atualmente [suportada pelo Azure Backup](sap-hana-backup-support-matrix.md#scenario-support)
- [Parar a proteção com dados](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) de retenção para a base de dados
- Realizar a atualização ou atualização
- Reexecutar o guião pré-inscrição. Normalmente, o processo de atualização remove as funções necessárias. Executar o script de pré-inscrição ajudará a verificar todas as funções necessárias
- Retomar a proteção da base de dados novamente

## <a name="sdc-to-mdc-upgrade-with-no-change-in-sid"></a>SDC para atualização MDC sem alteração no SID

As atualizações da SDC para a MDC que não causam uma alteração sid podem ser tratadas da seguinte forma:

- Certifique-se de que a nova versão MDC é atualmente [suportada pelo Azure Backup](sap-hana-backup-support-matrix.md#scenario-support)
- [Parar a proteção com dados](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) de retenção para a antiga base de dados sdc
- Faça a atualização. Após a conclusão, o sistema HANA é agora MDC com um sistema DB e DBs inquilinos
- Reexecutar o [roteiro de pré-inscrição](https://aka.ms/scriptforpermsonhana)
- Re-registe a extensão da mesma máquina no portal Azure (**Backup**  ->  **View detalhes** -> Selecione o registo de VM -> Azure relevante)
- Clique em **Redescobrir DBs** para o mesmo VM. Esta ação deve mostrar os novos DBs no passo 3 como SYSTEMDB e Tenant DB, não SDC
- A antiga base de dados da SDC continuará a existir no cofre e terá dados antigos retidos de acordo com a política
- Configure cópiade segurança para estas bases de dados

## <a name="sdc-to-mdc-upgrade-with-a-change-in-sid"></a>SDC para atualização MDC com uma mudança no SID

As atualizações da SDC para a MDC que causam uma alteração sid podem ser tratadas da seguinte forma:

- Certifique-se de que a nova versão MDC é atualmente [suportada pelo Azure Backup](sap-hana-backup-support-matrix.md#scenario-support)
- **Parar a proteção com dados** de retenção para a antiga base de dados sdc
- Faça a atualização. Após a conclusão, o sistema HANA é agora MDC com um sistema DB e DBs inquilinos
- Reexecutar o [script de pré-registo](https://aka.ms/scriptforpermsonhana) com detalhes corretos (novos SID e MDC). Devido a uma mudança no SID, poderá enfrentar problemas com a execução com sucesso do script. Contacte o suporte de backup Azure se enfrentar problemas.
- Re-registe a extensão da mesma máquina no portal Azure (**Backup**  ->  **View detalhes** -> Selecione o registo de VM -> Azure relevante)
- Clique em **Redescobrir DBs** para o mesmo VM. Esta ação deve mostrar os novos DBs no passo 3 como SYSTEMDB e Tenant DB, não SDC
- A antiga base de dados da SDC continuará a existir no cofre e tem dados antigos retidos de acordo com a política
- Configure cópiade segurança para estas bases de dados

## <a name="re-registration-failures"></a>Falhas de reregisto

Verifique se há um ou mais dos seguintes sintomas antes de desencadear a operação de reregisto:

- Todas as operações (tais como backup, restauro e cópia de segurança configurada) estão a falhar no VM com um dos seguintes códigos de erro: **WorkloadExtensionNotReachable, UserErrorWorkloadExtensionNotInstalled, WorkloadExtensionNotPresent, WorkloadExtensionDidntDequeueMsg**.
- Se a área de Estado de **Reserva** para o item de reserva estiver a mostrar **não alcançável,** exclua todas as outras causas que possam resultar no mesmo estado:

  - Falta de autorização para realizar operações relacionadas com backup no VM
  - O VM está desligado, por isso os reforços não podem ter lugar.
  - Problemas de rede

Estes sintomas podem surgir por uma ou mais das seguintes razões:

- Uma extensão foi eliminada ou desinstalada a partir do portal.
- O VM foi restaurado no tempo através da restauração do disco no local.
- O VM foi desligado por um período prolongado, pelo que a configuração da extensão expirou.
- O VM foi eliminado, e outro VM foi criado com o mesmo nome e no mesmo grupo de recursos que o VM eliminado.

Nos cenários anteriores, recomendamos que desencadeie uma nova operação de registo no VM.

## <a name="next-steps"></a>Passos seguintes

- Reveja as [perguntas frequentes](https://docs.microsoft.com/azure/backup/sap-hana-faq-backup-azure-vm) sobre o backup das bases de dados SAP HANA em VMs Azure.
