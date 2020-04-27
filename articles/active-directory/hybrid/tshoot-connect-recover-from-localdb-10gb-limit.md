---
title: 'Azure AD Connect: Como recuperar do problema limite de 10GB do LocalDB Microsoft Docs'
description: Este tópico descreve como recuperar o Serviço de Sincronização azure AD Connect quando encontra o problema do limite localDB de 10GB.
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
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/26/2020
ms.locfileid: "60386929"
---
# <a name="azure-ad-connect-how-to-recover-from-localdb-10-gb-limit"></a>Azure AD Connect: Como recuperar a partir do limite de 10 GB de LocalDB
O Azure AD Connect necessita de uma base de dados do SQL Server para armazenar dados de identidade. Pode utilizar a predefinição da LocalDB do SQL Server 2012 Express instalada com o Azure AD Connect ou utilizar o seu SQL completo. O SQL Server Express impõe um limite de tamanho de 10 GB. Ao utilizar a LocalDB e este limite ser atingido, o Serviço de Sincronização do Azure AD Connect já não pode iniciar ou sincronizar corretamente. Este artigo fornece os passos de recuperação.

## <a name="symptoms"></a>Sintomas
Há dois sintomas comuns:

* O Serviço de Sincronização de Ligação Azure AD está em **execução,** mas não sincroniza com um erro *"stop-database-full".*

* O Serviço de Sincronização de Ligação Azure AD **não consegue iniciar.** Quando tenta iniciar o serviço, falha com o evento 6323 e a mensagem de erro "O servidor encontrou um erro porque o *SQL Server está fora do espaço do disco."*

## <a name="short-term-recovery-steps"></a>Passos de recuperação a curto prazo
Esta secção fornece os passos para recuperar o espaço DB necessário para que o Serviço de Sincronização de Ligação AD Azure retome a operação. Os passos incluem:
1. [Determine o estado do Serviço de Sincronização](#determine-the-synchronization-service-status)
2. [Encolher a base de dados](#shrink-the-database)
3. [Eliminar dados de histórico de execução](#delete-run-history-data)
4. [Encurtar o período de retenção para os dados do histórico de execução](#shorten-retention-period-for-run-history-data)

### <a name="determine-the-synchronization-service-status"></a>Determine o estado do Serviço de Sincronização
Primeiro, determine se o Serviço de Sincronização ainda está em funcionamento ou não:

1. Inicie sessão no seu servidor Azure AD Connect como administrador.

2. Vá ao **Gestor de Controlo**de Serviços.

3. Verifique o estado do **Microsoft Azure AD Sync**.


4. Se estiver a funcionar, não pare nem reinicie o serviço. Ignore o passo [da base de dados](#shrink-the-database) e vá para eliminar o passo de dados de [histórico.](#delete-run-history-data)

5. Se não estiver a funcionar, tente iniciar o serviço. Se o serviço começar com sucesso, ignore o passo [da base de dados E](#shrink-the-database) vá para eliminar o passo de dados de [histórico.](#delete-run-history-data) Caso contrário, continue com encolher o passo [da base de dados.](#shrink-the-database)

### <a name="shrink-the-database"></a>Encolher a base de dados
Utilize a operação Shrink para libertar espaço DB suficiente para iniciar o Serviço de Sincronização. Liberta espaço DB removendo espaços brancos na base de dados. Este passo é o melhor esforço, pois não é garantido que sempre se possa recuperar espaço. Para saber mais sobre a operação Shrink, leia este artigo [Encolher uma base de dados.](https://msdn.microsoft.com/library/ms189035.aspx)

> [!IMPORTANT]
> Ignore este passo se conseguir que o Serviço de Sincronização seja executado. Não é aconselhável reduzir o SQL DB, uma vez que pode levar a um fraco desempenho devido ao aumento da fragmentação.

O nome da base de dados criada para Azure AD Connect é **ADSync**. Para efetuar uma operação De encolher, deve iniciar sessão como sindina ou DBO da base de dados. Durante a instalação Azure AD Connect, são concedidas as seguintes contas de direitos de sisadmina:
* Administradores Locais
* A conta de utilizador que foi utilizada para executar a instalação Azure AD Connect.
* A conta Sync Service que é usada como o contexto operacional do Serviço de Sincronização de Ligação Azure AD.
* O grupo local ADSyncAdmins que foi criado durante a instalação.

1. Volte a colocar a base de dados copiando ficheiros `%ProgramFiles%\Microsoft Azure AD Sync\Data` **ADSync.mdf** e **ADSync_log.ldf** localizados num local seguro.

2. Inicie uma nova sessão powerShell.

3. Navegar para `%ProgramFiles%\Microsoft SQL Server\110\Tools\Binn`pasta .

4. Inicie a utilidade **sqlcmd** executando o comando, `./SQLCMD.EXE -S "(localdb)\.\ADSync" -U <Username> -P <Password>`utilizando a credencial de uma sisadmina ou da base de dados DBO.

5. Para encolher a base de dados, no aviso de `DBCC Shrinkdatabase(ADSync,1);`sqlcmd (1>), insira, seguida na `GO` linha seguinte.

6. Se a operação for bem sucedida, tente voltar a iniciar o Serviço de Sincronização. Se conseguir iniciar o Serviço de Sincronização, vá ao Eliminar o passo de [dados do histórico.](#delete-run-history-data) Caso contrário, contacte o Suporte.

### <a name="delete-run-history-data"></a>Eliminar dados de histórico de execução
Por padrão, o Azure AD Connect mantém até sete dias de dados de histórico de execução. Neste passo, eliminamos os dados do histórico de execução para recuperar o espaço DB para que o Serviço de Sincronização de Ligação AD Azur do Azure possa voltar a sincronizar.

1. Inicie **o Gestor de Serviços** de Sincronização indo para o START → Serviço de Sincronização.

2. Vá ao separador **Operações.**

3. Under **Actions**, selecione **Clear Runs**...

4. Pode escolher **Clear todas as corridas** ou **clear runs antes... data \<>** opção. Recomenda-se que comece por limpar dados de histórico de execução com mais de dois dias. Se continuar a detetar problemas de tamanho DB, escolha a opção **Clear all runs.**

### <a name="shorten-retention-period-for-run-history-data"></a>Encurtar o período de retenção para os dados do histórico de execução
Este passo é reduzir a probabilidade de correr para o problema limite de 10 GB após vários ciclos de sincronização.

1. Abra uma nova sessão da PowerShell.

2. Executar `Get-ADSyncScheduler` e tomar nota da propriedade PurgeRunHistoryInterval, que especifica o período de retenção atual.

3. Corra `Set-ADSyncScheduler -PurgeRunHistoryInterval 2.00:00:00` para definir o período de retenção para dois dias. Ajuste o período de retenção conforme apropriado.

## <a name="long-term-solution--migrate-to-full-sql"></a>Solução a longo prazo - Migrar para o SQL completo
Em geral, o problema indica que o tamanho da base de dados de 10 GB já não é suficiente para que o Azure AD Connect sincronize o seu Diretório Ativo no local para a AD Azure. Recomenda-se que mude para a utilização da versão completa do servidor SQL. Não é possível substituir diretamente a LocalDB de uma implementação existente do Azure AD Connect pela base de dados da versão completa do SQL. Em vez disso, tem de implementar um novo servidor do Azure AD Connect com a versão completa do SQL. É recomendado que efetue uma migração rotativa, em que o novo servidor do Azure AD Connect (com a base de dados do SQL) seja implementado como um servidor de teste, ao lado do servidor do Azure AD Connect existente (com a LocalDB). 
* Para obter instruções sobre como configurar o SQL remoto com o Azure AD Connect, consulte o artigo [Instalação personalizada do Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-custom).
* Para instruções sobre a migração rotativa de atualização do Azure AD Connect, consulte o artigo [Azure AD Connect: atualizar de uma versão anterior para a versão mais recente](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version#swing-migration).

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md).
