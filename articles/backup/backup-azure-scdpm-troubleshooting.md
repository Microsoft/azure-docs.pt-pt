---
title: Resolver problemas relacionados com o System Center Data Protection Manager com o Azure Backup
description: Resolva problemas no System Center Data Protection Manager.
services: backup
author: kasinh
manager: vvithal
ms.service: backup
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: kasinh
ms.openlocfilehash: 4108616e3ae41e2c88b74bb08d5f846c0035101f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60236203"
---
# <a name="troubleshoot-system-center-data-protection-manager"></a>Resolver Problemas do System Center Data Protection Manager

Este artigo descreve as soluções para problemas que poderá encontrar ao utilizar o Data Protection Manager.

Para obter notas de versão mais recente para o System Center Data Protection Manager, veja a [documentação do System Center](https://docs.microsoft.com/system-center/dpm/dpm-release-notes?view=sc-dpm-2016). Pode saber mais sobre o suporte para o Data Protection Manager no [essa matriz](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-2016).


## <a name="error-replica-is-inconsistent"></a>Erro: A réplica está inconsistente

Uma réplica pode ser inconsistente pelos seguintes motivos:
- A tarefa de criação de réplica falhar.
- Existem problemas com o diário de alterações.
- O bitmap do filtro de nível de volume contém erros.
- A máquina de origem for encerrado inesperadamente.
- O log de sincronização estoura.
- A réplica está inconsistente verdadeiramente.

Para resolver este problema, execute as seguintes ações:
- Para remover o estado inconsistente, execute a verificação de consistência manualmente ou agendar uma verificação de consistência diária.
- Certifique-se de que está a utilizar a versão mais recente do Microsoft Azure Backup Server e Data Protection Manager.
- Certifique-se de que o **consistência automáticas** definição está ativada.
- Tente reiniciar os serviços no prompt de comando. Utilize o `net stop dpmra` comando seguido `net start dpmra`.
- Certifique-se de que está a cumprir os requisitos de conectividade e largura de banda de rede.
- Verifique se a máquina de origem foi encerrada inesperadamente.
- Certifique-se de que o disco está em bom estado e que existe espaço suficiente para a réplica.
- Certifique-se de que não há nenhuma tarefa de cópia de segurança duplicada que está em execução em simultâneo.

## <a name="error-online-recovery-point-creation-failed"></a>Erro: Falha na criação do ponto de recuperação online

Para resolver este problema, execute as seguintes ações:
- Certifique-se de que está a utilizar a versão mais recente do agente do Backup do Azure.
- Tente criar manualmente um ponto de recuperação na área de tarefas de proteção.
- Certifique-se de que execute uma verificação de consistência na origem de dados.
- Certifique-se de que está a cumprir os requisitos de conectividade e largura de banda de rede.
- Quando os dados de réplica estão num estado inconsistente, crie um ponto de recuperação do disco desta origem de dados.
- Certifique-se de que a réplica está presente e não tem em falta.
- Certifique-se de que a réplica tem espaço suficiente para criar o diário de número (USN) de sequência de atualização.

## <a name="error-unable-to-configure-protection"></a>Erro: Não é possível configurar a proteção

Este erro ocorre quando o servidor do Data Protection Manager não consegue contactar o servidor protegido. 

Para resolver este problema, execute as seguintes ações:
- Certifique-se de que está a utilizar a versão mais recente do agente do Backup do Azure.
- Certifique-se de que existe conectividade (rede/firewall/proxy) entre o servidor do Data Protection Manager e o servidor protegido.
- Se estiver a proteger um SQL server, certifique-se de que o **propriedades de início de sessão** > **NT AUTHORITY\SYSTEM** propriedade mostra o **sysadmin** definição ativada.

## <a name="error-server-not-registered-as-specified-in-vault-credential-file"></a>Erro: Servidor não registado conforme especificado no ficheiro de credenciais do Cofre

Este erro ocorre durante o processo de recuperação para dados do servidor de cópia de segurança do Data Protection Manager/do Azure. O ficheiro de credenciais de cofre que é utilizado no processo de recuperação não pertence ao Cofre de serviços de recuperação para o servidor de cópia de segurança do Data Protection Manager/do Azure.

Para resolver este problema, execute estes passos:
1. Transferir o ficheiro de credenciais do cofre a partir do Cofre de serviços de recuperação para o qual o servidor de cópia de segurança do Data Protection Manager/Azure está registado.
2. Tente registar o servidor com o cofre com o ficheiro de credenciais do cofre transferido mais recentemente.

## <a name="error-no-recoverable-data-or-selected-server-not-a-data-protection-manager-server"></a>Erro: Não existem dados recuperáveis ou o servidor selecionado não um servidor do Data Protection Manager

Este erro ocorre pelos seguintes motivos:
- Não existem outros servidores de cópia de segurança do Data Protection Manager/Azure são registados no Cofre de serviços de recuperação.
- Os servidores ainda ainda não carregou os metadados.
- O servidor selecionado não é um servidor de cópia de segurança do Data Protection Manager/do Azure.

Quando outros servidores de cópia de segurança do Data Protection Manager/Azure são registados no Cofre de serviços de recuperação, execute estes passos para resolver o problema:
1. Certifique-se de que o agente de cópia de segurança do Azure mais recente está instalado.
2. Depois de assegurar que o agente mais recente está instalado, aguarde um dia antes de iniciar o processo de recuperação. A tarefa de cópia de segurança noturna carrega os metadados para todas as cópias de segurança protegidas para a cloud. Os dados de cópia de segurança, em seguida, estão disponíveis para recuperação.

## <a name="error-provided-encryption-passphrase-doesnt-match-passphrase-for-server"></a>Erro: Frase de acesso de encriptação fornecido não corresponde à frase de acesso para servidor

Este erro ocorre durante o processo de encriptação quando recuperar dados do Data Protection Manager/Azure Backup server. A frase de acesso de encriptação que é utilizado no processo de recuperação não corresponde à frase de acesso de encriptação do servidor. Como resultado, o agente não é possível desencriptar os dados e a recuperação falhará.

> [!IMPORTANT]
> Se esquece ou perde a frase de acesso de encriptação, não há nenhum outros métodos para recuperar os dados. A única opção é voltar a gerar a frase de acesso. Utilize a nova frase de acesso para encriptar os dados de cópia de segurança futuros.
>
> Quando estiver a recuperar dados, sempre forneça a frase de acesso de encriptação mesmo que tenha associado ao servidor de cópia de segurança do Data Protection Manager/do Azure. 
>
