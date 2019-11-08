---
title: Solucionar problemas do System Center Data Protection Manager-backup do Azure
description: Neste artigo, descubra soluções para problemas que você pode encontrar ao usar o System Center Data Protection Manager.
ms.reviewer: kasinh
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: dacurwin
ms.openlocfilehash: 1ff6db463821780975f855488c51663c6a75f83a
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73747322"
---
# <a name="troubleshoot-system-center-data-protection-manager"></a>Resolver Problemas do System Center Data Protection Manager

Este artigo descreve soluções para problemas que você pode encontrar ao usar Data Protection Manager.

Para obter as notas de versão mais recentes do System Center Data Protection Manager, consulte a [documentação do System Center](https://docs.microsoft.com/system-center/dpm/dpm-release-notes?view=sc-dpm-2016). Você pode saber mais sobre o suporte para Data Protection Manager nesta [matriz](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-2016).

## <a name="error-replica-is-inconsistent"></a>Erro: a réplica está inconsistente

Uma réplica pode ser inconsistente pelos seguintes motivos:

- Falha no trabalho de criação de réplica.
- Há problemas com o diário de alterações.
- O bitmap de filtro de nível de volume contém erros.
- O computador de origem é desligado inesperadamente.
- O log de sincronização estoura.
- A réplica está verdadeiramente inconsistente.

Para resolver esse problema, execute as seguintes ações:

- Para remover o status inconsistente, execute a verificação de consistência manualmente ou agende uma verificação de consistência diária.
- Verifique se você está usando a versão mais recente do Backup do Microsoft Azure Server e Data Protection Manager.
- Verifique se a configuração de **consistência automática** está habilitada.
- Tente reiniciar os serviços no prompt de comando. Use o comando `net stop dpmra` seguido por `net start dpmra`.
- Verifique se você está atendendo aos requisitos de conectividade e largura de banda da rede.
- Verifique se o computador de origem foi desligado inesperadamente.
- Verifique se o disco está íntegro e se há espaço suficiente para a réplica.
- Verifique se não há nenhum trabalho de backup duplicado em execução simultaneamente.

## <a name="error-online-recovery-point-creation-failed"></a>Erro: falha na criação do ponto de recuperação online

Para resolver esse problema, execute as seguintes ações:

- Verifique se você está usando a versão mais recente do agente de backup do Azure.
- Tente criar um ponto de recuperação manualmente na área de tarefa proteção.
- Certifique-se de executar uma verificação de consistência na fonte de dados.
- Verifique se você está atendendo aos requisitos de conectividade e largura de banda da rede.
- Quando os dados da réplica estiverem em um estado inconsistente, crie um ponto de recuperação de disco dessa fonte de dados.
- Verifique se a réplica está presente e não está ausente.
- Verifique se a réplica tem espaço suficiente para criar o diário de número de sequência de atualização (USN).

## <a name="error-unable-to-configure-protection"></a>Erro: não é possível configurar a proteção

Esse erro ocorre quando o servidor de Data Protection Manager não pode contatar o servidor protegido.

Para resolver esse problema, execute as seguintes ações:

- Verifique se você está usando a versão mais recente do agente de backup do Azure.
- Verifique se há conectividade (rede/firewall/proxy) entre o servidor de Data Protection Manager e o servidor protegido.
- Se você estiver protegendo um SQL Server, verifique se as **Propriedades de logon** > propriedade **NT AUTHORITY\SYSTEM** mostra a configuração **sysadmin** habilitada.

## <a name="error-server-not-registered-as-specified-in-vault-credential-file"></a>Erro: servidor não registrado como especificado no arquivo de credencial do cofre

Esse erro ocorre durante o processo de recuperação para dados do servidor de backup Data Protection Manager/do Azure. O arquivo de credencial do cofre que é usado no processo de recuperação não pertence ao cofre dos serviços de recuperação para o Data Protection Manager/servidor de backup do Azure.

Para resolver esse problema, execute estas etapas:

1. Baixe o arquivo de credencial do cofre do cofre dos serviços de recuperação no qual o servidor de backup do Data Protection Manager/Azure está registrado.
2. Tente registrar o servidor com o cofre usando o arquivo de credencial do cofre baixado mais recentemente.

## <a name="error-no-recoverable-data-or-selected-server-not-a-data-protection-manager-server"></a>Erro: nenhum dado recuperável ou servidor selecionado não é um servidor Data Protection Manager

Esse erro ocorre pelos seguintes motivos:

- Nenhum outro servidor de backup Data Protection Manager/Azure está registrado no cofre dos serviços de recuperação.
- Os servidores ainda não carregaram os metadados.
- O servidor selecionado não é um servidor de backup Data Protection Manager/do Azure.

Quando outros servidores de backup do Data Protection Manager/do Azure estiverem registrados no cofre dos serviços de recuperação, execute estas etapas para resolver o problema:

1. Verifique se o agente de backup do Azure mais recente está instalado.
2. Depois de garantir que o agente mais recente esteja instalado, aguarde um dia antes de iniciar o processo de recuperação. O trabalho de backup noturno carrega os metadados de todos os backups protegidos para a nuvem. Os dados de backup ficam disponíveis para recuperação.

## <a name="error-provided-encryption-passphrase-doesnt-match-passphrase-for-server"></a>Erro: a senha de criptografia fornecida não corresponde à senha do servidor

Esse erro ocorre durante o processo de criptografia ao recuperar dados do servidor de backup Data Protection Manager/do Azure. A senha de criptografia usada no processo de recuperação não corresponde à senha de criptografia do servidor. Como resultado, o agente não pode descriptografar os dados e a recuperação falha.

> [!IMPORTANT]
> Se você esquecer ou perder a senha de criptografia, não haverá nenhum outro método para recuperar os dados. A única opção é regenerar a frase secreta. Use a nova senha para criptografar dados de backup futuros.
>
> Quando você estiver recuperando dados, sempre forneça a mesma senha de criptografia associada ao Data Protection Manager/servidor de backup do Azure.
>
