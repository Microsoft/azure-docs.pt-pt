---
title: 'Azure AD Connect: Como recuperar do LocalDB problema 10 GB | Documentos da Microsoft'
description: Este tópico descreve como recuperar o serviço do Azure AD Connect sincronização quando encontrar 10GB de LocalDB limitar o problema.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 41d081af-ed89-4e17-be34-14f7e80ae358
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d420c64c5834f7d3cb11d2f5f59e3ed85a54891
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58435605"
---
# <a name="azure-ad-connect-how-to-recover-from-localdb-10-gb-limit"></a>Azure AD Connect: Como recuperar a partir do limite de 10 GB de LocalDB
O Azure AD Connect necessita de uma base de dados do SQL Server para armazenar dados de identidade. Pode utilizar a predefinição da LocalDB do SQL Server 2012 Express instalada com o Azure AD Connect ou utilizar o seu SQL completo. O SQL Server Express impõe um limite de tamanho de 10 GB. Ao utilizar a LocalDB e este limite ser atingido, o Serviço de Sincronização do Azure AD Connect já não pode iniciar ou sincronizar corretamente. Este artigo fornece os passos de recuperação.

## <a name="symptoms"></a>Sintomas
Existem dois sintomas comuns:

* Serviço do Azure AD Connect sincronização **está em execução** mas não conseguir sincronizar com *"parada-base de dados-disco cheio"* erro.

* Serviço do Azure AD Connect sincronização **não consegue iniciar**. Quando tenta iniciar o serviço, ele falhará com eventos 6323 e mensagem de erro *"o servidor encontrou um erro porque o SQL Server está fora do espaço em disco."*

## <a name="short-term-recovery-steps"></a>Passos de recuperação de curto prazo
Esta secção fornece os passos para recuperar espaço da base necessário para o serviço do Azure AD Connect sincronização retomar a operação. Os passos incluem:
1. [Determinar o estado do serviço de sincronização](#determine-the-synchronization-service-status)
2. [Encolher a base de dados](#shrink-the-database)
3. [Eliminar dados de histórico de execução](#delete-run-history-data)
4. [Reduzir o período de retenção de dados do histórico de execuções](#shorten-retention-period-for-run-history-data)

### <a name="determine-the-synchronization-service-status"></a>Determinar o estado do serviço de sincronização
Em primeiro lugar, determine se o serviço de sincronização ainda está em execução ou não:

1. Inicie sessão no seu servidor do Azure AD Connect como administrador.

2. Aceda a **Gestor de controlo de serviço**.

3. Verifique o estado dos **Microsoft Azure AD Sync**.


4. Se estiver em execução, não parar ou reiniciar o serviço. Ignorar [encolher a base de dados](#shrink-the-database) passo e aceda à [eliminar dados de histórico de executar](#delete-run-history-data) passo.

5. Se não está em execução, tente iniciar o serviço. Se o serviço é iniciado com êxito, ignore [encolher a base de dados](#shrink-the-database) passo e aceda à [eliminar dados de histórico de executar](#delete-run-history-data) passo. Caso contrário, continue com [encolher a base de dados](#shrink-the-database) passo.

### <a name="shrink-the-database"></a>Encolher a base de dados
Use a operação de redução para libertar espaço suficiente DB para iniciar o serviço de sincronização. Libera um espaço de DB ao remover os espaços em branco na base de dados. Este passo é melhor esforço, pois não é garantido que sempre é possível recuperar espaço. Para saber mais sobre a operação de redução, leia este artigo [reduzir uma base de dados](https://msdn.microsoft.com/library/ms189035.aspx).

> [!IMPORTANT]
> Se pode obter o serviço de sincronização para ser executado, ignore este passo. Não é recomendado reduzir o banco de dados SQL pode levar a fraco desempenho devido a maior fragmentação.

O nome da base de dados criada para o Azure AD Connect é **ADSync**. Para efetuar uma operação de redução, tem de iniciar sessão como o administrador do sistema ou DBO da base de dados. Durante a instalação do Azure AD Connect, as seguintes contas são concedidas direitos de administrador do sistema:
* Administradores locais
* A conta de utilizador que foi utilizada para executar a instalação do Azure AD Connect.
* A conta de serviço de sincronização que é utilizada como o contexto de operação de serviço do Azure AD Connect sincronização.
* O grupo ADSyncAdmins que foi criado durante a instalação local.

1. Cópia de segurança da base de dados ao copiar **ADSync.mdf** e **ADSync_log.ldf** ficheiros localizados em `%ProgramFiles%\Microsoft Azure AD Sync\Data` numa localização segura.

2. Inicie uma nova sessão do PowerShell.

3. Navegue até à pasta `%ProgramFiles%\Microsoft SQL Server\110\Tools\Binn`.

4. Inicie **sqlcmd** utilitário ao executar o comando `./SQLCMD.EXE -S "(localdb)\.\ADSync" -U <Username> -P <Password>`, usando a credencial de um administrador do sistema ou a base de dados DBO.

5. Para reduzir a base de dados, a linha de comandos sqlcmd (1 >), introduza `DBCC Shrinkdatabase(ADSync,1);`, seguido `GO` na linha seguinte.

6. Se a operação for concluída com êxito, tente reiniciar o serviço de sincronização. Se pode iniciar o serviço de sincronização, aceda a [eliminar dados de histórico de executar](#delete-run-history-data) passo. Caso contrário, contacte o suporte.

### <a name="delete-run-history-data"></a>Eliminar dados de histórico de execução
Por predefinição, o Azure AD Connect mantém até dias sete de dados do histórico de execuções. Neste passo, vamos eliminar os dados de histórico de execução para recuperar espaço da base para que o serviço do Azure AD Connect sincronização pode iniciar a sincronização novamente.

1. Inicie **Synchronization Service Manager** ao aceder ao serviço de sincronização de início →.

2. Vá para o **operações** separador.

3. Sob **ações**, selecione **execuções clara**...

4. Pode optar por **desmarque todas as execuções** ou **Clear é executado antes de... \<data >** opção. Recomenda-se que comece por limpar dados de histórico que têm mais de dois dias de executar. Se continuar a deparar com problema de tamanho de DB, em seguida, escolha o **desmarque todas as execuções** opção.

### <a name="shorten-retention-period-for-run-history-data"></a>Reduzir o período de retenção de dados do histórico de execuções
Este passo é reduzir a probabilidade de em execução para o problema de limite de 10 GB após vários ciclos de sincronização.

1. Abra uma nova sessão do PowerShell.

2. Executar `Get-ADSyncScheduler` e tome nota da propriedade PurgeRunHistoryInterval, que especifica o período de retenção atual.

3. Executar `Set-ADSyncScheduler -PurgeRunHistoryInterval 2.00:00:00` para definir o período de retenção para dois dias. Ajuste o período de retenção conforme apropriado.

## <a name="long-term-solution--migrate-to-full-sql"></a>Solução a longo prazo – migrar para o SQL completo
Em geral, o problema é indicativo-se de que o tamanho da base de dados de 10 GB já não é suficiente para o Azure AD Connect sincronizar o diretório de Active Directory no local para o Azure AD. Recomenda-se que mude para utilizar a versão completa do SQL server. Não é possível substituir diretamente a LocalDB de uma implementação existente do Azure AD Connect pela base de dados da versão completa do SQL. Em vez disso, tem de implementar um novo servidor do Azure AD Connect com a versão completa do SQL. É recomendado que efetue uma migração rotativa, em que o novo servidor do Azure AD Connect (com a base de dados do SQL) seja implementado como um servidor de teste, ao lado do servidor do Azure AD Connect existente (com a LocalDB). 
* Para obter instruções sobre como configurar o SQL remoto com o Azure AD Connect, consulte o artigo [Instalação personalizada do Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-custom).
* Para obter instruções sobre mudar a migração para a atualização do Azure AD Connect, veja o artigo [Azure AD Connect: Upgrade from a previous version to the latest](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version#swing-migration) (Sincronização do Azure AD Connect: Atualizar de uma versão anterior para a mais recente).

## <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md).
