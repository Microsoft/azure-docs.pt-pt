---
title: 'Azure AD Connect: Como recuperar da emissão limite localDB 10GB / Microsoft Docs'
description: Este tópico descreve como recuperar o Serviço de Sincronização AZure AD Connect Quando encontra o problema de limite localDB de 10GB.
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
ms.topic: troubleshooting
ms.date: 07/17/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7ca5361d8500ecd4ea22a577d0a4dc7ced606eab
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89275907"
---
# <a name="azure-ad-connect-how-to-recover-from-localdb-10-gb-limit"></a>Azure AD Connect: como recuperar do limite de 10 GB da LocalDB
O Azure AD Connect necessita de uma base de dados do SQL Server para armazenar dados de identidade. Pode utilizar a predefinição da LocalDB do SQL Server 2012 Express instalada com o Azure AD Connect ou utilizar o seu SQL completo. O SQL Server Express impõe um limite de tamanho de 10 GB. Ao utilizar a LocalDB e este limite ser atingido, o Serviço de Sincronização do Azure AD Connect já não pode iniciar ou sincronizar corretamente. Este artigo fornece os passos de recuperação.

## <a name="symptoms"></a>Sintomas
Há dois sintomas comuns:

* O Serviço de Sincronização AD Connect Connect **está em funcionamento,** mas não sincroniza com o erro *"stop-database-disk-full".*

* O Serviço de Sincronização AD Connect AD **não pode iniciar**. Quando tenta iniciar o serviço, falha com o evento 6323 e a mensagem de erro "O servidor encontrou um erro porque o *SQL Server está fora do espaço do disco."*

## <a name="short-term-recovery-steps"></a>Passos de recuperação a curto prazo
Esta secção fornece os passos para recuperar o espaço DB necessário para que o Serviço de Sincronização AD Connect Connect para retomar o funcionamento. Os passos incluem:
1. [Determinar o estado do Serviço de Sincronização](#determine-the-synchronization-service-status)
2. [Encolher a base de dados](#shrink-the-database)
3. [Eliminar dados de histórico de execução](#delete-run-history-data)
4. [Encurtar o período de retenção para os dados do histórico de execução](#shorten-retention-period-for-run-history-data)

### <a name="determine-the-synchronization-service-status"></a>Determinar o estado do Serviço de Sincronização
Em primeiro lugar, determinar se o Serviço de Sincronização ainda está em funcionamento ou não:

1. Inicie sessão no seu servidor Azure AD Connect como administrador.

2. Vá ao **Gestor de Controlo de Serviços.**

3. Verifique o estado do **Microsoft Azure AD Sync**.


4. Se estiver em funcionamento, não pare nem reinicie o serviço. Ignore o passo [da base de dados](#shrink-the-database) e vá para eliminar o passo [de dados de histórico.](#delete-run-history-data)

5. Se não estiver a funcionar, tente iniciar o serviço. Se o serviço começar com sucesso, ignore [Shrink the database](#shrink-the-database) step e vá para Eliminar executar passo de [dados de histórico.](#delete-run-history-data) Caso contrário, continue com [a redução do](#shrink-the-database) passo da base de dados.

### <a name="shrink-the-database"></a>Encolher a base de dados
Utilize a operação Descosudo para libertar espaço DB suficiente para iniciar o Serviço de Sincronização. Liberta espaço DB removendo espaços brancos na base de dados. Este passo é o melhor esforço, pois não é garantido que possa sempre recuperar espaço. Para saber mais sobre a operação Diminuir a sua operação, leia este artigo Leia este artigo [Diminuir a sua vez.](/sql/relational-databases/databases/shrink-a-database?view=sql-server-ver15)

> [!IMPORTANT]
> Ignore este passo se conseguir que o Serviço de Sincronização seja executado. Não é aconselhável encolher o SQL DB, uma vez que pode levar a um fraco desempenho devido ao aumento da fragmentação.

O nome da base de dados criada para Azure AD Connect é **ADSync**. Para realizar uma operação de encolher, tem de iniciar sessão como sysadmin ou DBO da base de dados. Durante a instalação Azure AD Connect, são concedidos direitos sysadmin:
* Administradores Locais
* A conta de utilizador que foi utilizada para executar a instalação Azure AD Connect.
* A conta Sync Service que é utilizada como o contexto operacional do Serviço de Sincronização AD Connect.
* O grupo local ADSyncAdmins que foi criado durante a instalação.

1. Ressalta a base de dados copiando ficheiros **ADSync.mdf** e **ADSync_log.ldf** localizados sob `%ProgramFiles%\Microsoft Azure AD Sync\Data` um local seguro.

2. Inicie uma nova sessão powerShell.

3. Navegue para a pasta `%ProgramFiles%\Microsoft SQL Server\110\Tools\Binn` .

4. Inicie a utilidade **sqlcmd** executando o `./SQLCMD.EXE -S "(localdb)\.\ADSync" -U <Username> -P <Password>` comando, utilizando a credencial de um sysadmin ou da base de dados DBO.

5. Para encolher a base de dados, na 2lcmd (1>), `DBCC Shrinkdatabase(ADSync,1);` entre, seguida `GO` da linha seguinte.

6. Se a operação for bem sucedida, tente recomeçar o Serviço de Sincronização. Se conseguir iniciar o Serviço de Sincronização, vá para eliminar o passo de dados de histórico de [execução.](#delete-run-history-data) Caso contrário, contacte o Suporte.

### <a name="delete-run-history-data"></a>Eliminar dados de histórico de execução
Por padrão, o Azure AD Connect retém até sete dias de dados de histórico de execução. Neste passo, eliminamos os dados do histórico de execução para recuperar o espaço DB para que o Serviço de Sincronização AD Connect Connect Azure possa voltar a sincronizar.

1. Inicie **o Gestor de Serviços de Sincronização** indo ao START → Serviço de Sincronização.

2. Vá ao separador **operações.**

3. Em **Ações**, selecione **Clear Runs**...

4. Pode escolher **Limpar todas as corridas** ou **limpar as \<date> corridas antes...** opção. Recomenda-se que comece por limpar dados do histórico de execução com mais de dois dias. Se continuar a encontrar um problema de tamanho DB, então escolha a opção **Clear all runs.**

### <a name="shorten-retention-period-for-run-history-data"></a>Encurtar o período de retenção para os dados do histórico de execução
Este passo é reduzir a probabilidade de escorrê-lo para a questão do limite de 10 GB após vários ciclos de sincronização.

1. Abra uma nova sessão PowerShell.

2. Corra `Get-ADSyncScheduler` e tome nota da propriedade PurgeRunHistoryInterval, que especifica o período de retenção atual.

3. Corra `Set-ADSyncScheduler -PurgeRunHistoryInterval 2.00:00:00` para definir o período de retenção para dois dias. Ajuste o período de retenção conforme apropriado.

## <a name="long-term-solution--migrate-to-full-sql"></a>Solução de longo prazo – Migrar para o SQL completo
Em geral, o problema é indicativo de que o tamanho da base de dados de 10 GB já não é suficiente para o Azure AD Connect sincronizar o seu Ative Directory para Azure AD. Recomenda-se que mude a utilizar a versão completa do servidor SQL. Não é possível substituir diretamente a LocalDB de uma implementação existente do Azure AD Connect pela base de dados da versão completa do SQL. Em vez disso, tem de implementar um novo servidor do Azure AD Connect com a versão completa do SQL. É recomendado que efetue uma migração rotativa, em que o novo servidor do Azure AD Connect (com a base de dados do SQL) seja implementado como um servidor de teste, ao lado do servidor do Azure AD Connect existente (com a LocalDB). 
* Para obter instruções sobre como configurar o SQL remoto com o Azure AD Connect, consulte o artigo [Instalação personalizada do Azure AD Connect](./how-to-connect-install-custom.md).
* Para instruções sobre a migração rotativa de atualização do Azure AD Connect, consulte o artigo [Azure AD Connect: atualizar de uma versão anterior para a versão mais recente](./how-to-upgrade-previous-version.md#swing-migration).

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md).