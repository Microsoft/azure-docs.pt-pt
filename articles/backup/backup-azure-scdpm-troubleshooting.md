---
title: Resolver Problemas do System Center Data Protection Manager
description: Neste artigo, descubra soluções para problemas que possa encontrar durante a utilização do Gestor de Proteção de Dados do Centro de Sistema.
ms.topic: troubleshooting
ms.date: 01/30/2019
ms.openlocfilehash: 84646f34d905b570855f655465529eb19b717cab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "86513817"
---
# <a name="troubleshoot-system-center-data-protection-manager"></a>Resolver Problemas do System Center Data Protection Manager

Este artigo descreve soluções para problemas que poderá encontrar durante a utilização do Gestor de Proteção de Dados.

Para obter as últimas notas de lançamento do Gestor de Proteção de Dados do Centro de Sistema, consulte a [documentação](/system-center/dpm/dpm-release-notes)do System Center . Pode saber mais sobre o suporte ao Gestor de Proteção de Dados [nesta matriz.](/system-center/dpm/dpm-protection-matrix)

## <a name="error-replica-is-inconsistent"></a>Erro: A réplica é inconsistente

Uma réplica pode ser inconsistente pelas seguintes razões:

- O trabalho de criação de réplicas falha.
- Há problemas com o diário de mudanças.
- O mapa de bitm do filtro de nível de volume contém erros.
- A máquina de origem desliga-se inesperadamente.
- O registo de sincronização transborda.
- A réplica é verdadeiramente inconsistente.

Para resolver esta questão, execute as seguintes ações:

- Para remover o estado inconsistente, verifique a verificação de consistência manualmente ou agende uma verificação de consistência diária.
- Certifique-se de que está a utilizar a versão mais recente do Microsoft Azure Backup Server e do Data Protection Manager.
- Certifique-se de que a regulação **de consistência automática** está ativada.
- Tente reiniciar os serviços a partir do aviso de comando. Utilize o `net stop dpmra` comando seguido por `net start dpmra` .
- Certifique-se de que está a cumprir os requisitos de conectividade da rede e largura de banda.
- Verifique se a máquina de origem foi desligada inesperadamente.
- Certifique-se de que o disco está saudável e que há espaço suficiente para a réplica.
- Certifique-se de que não existem trabalhos de reserva duplicados que estejam a funcionar simultaneamente.

## <a name="error-online-recovery-point-creation-failed"></a>Erro: A criação de ponto de recuperação online falhou

Para resolver esta questão, execute as seguintes ações:

- Certifique-se de que está a utilizar a versão mais recente do agente Azure Backup.
- Tente criar manualmente um ponto de recuperação na área de tarefa de proteção.
- Certifique-se de que verifica a coerência da fonte de dados.
- Certifique-se de que está a cumprir os requisitos de conectividade da rede e largura de banda.
- Quando os dados da réplica estiverem em estado inconsistente, crie um ponto de recuperação de disco desta fonte de dados.
- Certifique-se de que a réplica está presente e não está desaparecida.
- Certifique-se de que a réplica tem espaço suficiente para criar o diário de número de sequência de atualização (USN).

## <a name="error-unable-to-configure-protection"></a>Erro: Incapaz de configurar a proteção

Este erro ocorre quando o servidor Gestor de Proteção de Dados não pode entrar em contacto com o servidor protegido.

Para resolver esta questão, execute as seguintes ações:

- Certifique-se de que está a utilizar a versão mais recente do agente Azure Backup.
- Certifique-se de que existe conectividade (rede/firewall/proxy) entre o servidor Do Gestor de Proteção de Dados e o servidor protegido.
- Se estiver a proteger um servidor SQL, certifique-se de que a propriedade **de Login Properties**  >  **NT AUTHORITY\SYSTEM** mostra a definição de **sysadmin** ativada.

## <a name="error-server-not-registered-as-specified-in-vault-credential-file"></a>Erro: Servidor não registado como especificado no ficheiro credencial de abóbada

Este erro ocorre durante o processo de recuperação dos dados do servidor de proteção de dados/Azure Backup. O ficheiro de credencial do cofre que é usado no processo de recuperação não pertence ao cofre dos Serviços de Recuperação para o servidor de backup do Data Protection Manager/Azure.

Para resolver esta questão, execute estes passos:

1. Descarregue o ficheiro de credencial do cofre do cofre dos Serviços de Recuperação para o qual está registado o servidor de backup de dados/Azure.
2. Tente registar o servidor com o cofre usando o ficheiro credencial de cofre mais recentemente descarregado.

## <a name="error-no-recoverable-data-or-selected-server-not-a-data-protection-manager-server"></a>Erro: Nenhum dado recuperável ou servidor selecionado não um servidor do Gestor de Proteção de Dados

Este erro ocorre pelas seguintes razões:

- Nenhum outro Gestor de Proteção de Dados/Servidores de Backup Azure estão registados no cofre dos Serviços de Recuperação.
- Os servidores ainda não carregaram os metadados.
- O servidor selecionado não é um Gestor de Proteção de Dados/Servidor de Backup Azure.

Quando outros servidores de Backup/Azure De proteção de dados estiverem registados no cofre dos Serviços de Recuperação, execute estes passos para resolver o problema:

1. Certifique-se de que o mais recente agente de backup da Azure está instalado.
2. Depois de garantir que o último agente está instalado, aguarde um dia antes de iniciar o processo de recuperação. O trabalho de backup noturno envia os metadados para todas as cópias de segurança protegidas para a nuvem. Os dados de backup estão então disponíveis para recuperação.

## <a name="error-provided-encryption-passphrase-doesnt-match-passphrase-for-server"></a>Erro: Desde que a palavra-passe de encriptação não corresponda à palavra-passe para o servidor

Este erro ocorre durante o processo de encriptação ao recuperar dados do servidor de proteção de dados/Azure Backup. A palavra-passe de encriptação que é usada no processo de recuperação não corresponde à palavra-passe de encriptação do servidor. Como resultado, o agente não pode desencriptar os dados e a recuperação falha.

> [!IMPORTANT]
> Se esquecer ou perder a palavra-passe de encriptação, não existem outros métodos para recuperar os dados. A única opção é regenerar a palavra-passe. Use a nova palavra-passe para encriptar futuros dados de backup.
>
> Quando estiver a recuperar dados, forneça sempre a mesma palavra-passe de encriptação que está associada ao servidor de Backup Data Protection/Azure.
>
