---
title: Resolver Problemas do System Center Data Protection Manager
description: Neste artigo, descubra soluções para problemas que possa encontrar ao utilizar o System Center Data Protection Manager.
ms.topic: troubleshooting
ms.date: 01/30/2019
ms.openlocfilehash: bcb30fa7eb3e05099761fc751b09a9fb16134e34
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75664756"
---
# <a name="troubleshoot-system-center-data-protection-manager"></a>Resolver Problemas do System Center Data Protection Manager

Este artigo descreve soluções para problemas que pode encontrar ao utilizar o Gestor de Proteção de Dados.

Para obter as mais recentes notas de lançamento para System Center Data Protection Manager, consulte a [documentação](https://docs.microsoft.com/system-center/dpm/dpm-release-notes?view=sc-dpm-2016)do System Center . Pode saber mais sobre o suporte para O Gestor de Proteção de Dados [nesta matriz.](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-2016)

## <a name="error-replica-is-inconsistent"></a>Erro: Réplica é inconsistente

Uma réplica pode ser inconsistente pelas seguintes razões:

- O trabalho de criação de réplicas falha.
- Há problemas com o jornal de mudança.
- O bitmap do filtro de nível de volume contém erros.
- A máquina de origem desliga-se inesperadamente.
- O tronco de sincronização transborda.
- A réplica é verdadeiramente inconsistente.

Para resolver esta questão, execute as seguintes ações:

- Para remover o estado inconsistente, faça a verificação de consistência manualmente ou agende uma verificação de consistência diária.
- Certifique-se de que está a utilizar a versão mais recente do Microsoft Azure Backup Server e do Data Protection Manager.
- Certifique-se de que a definição de **consistência automática** está ativada.
- Tente reiniciar os serviços a partir do pedido de comando. Utilize `net stop dpmra` o comando `net start dpmra`seguido de .
- Certifique-se de que está a cumprir os requisitos de conectividade da rede e largura de banda.
- Verifique se a máquina de origem foi desligada inesperadamente.
- Certifique-se de que o disco está saudável e que há espaço suficiente para a réplica.
- Certifique-se de que não existem trabalhos de backup duplicados que estejam a funcionar simultaneamente.

## <a name="error-online-recovery-point-creation-failed"></a>Erro: A criação de pontos de recuperação online falhou

Para resolver esta questão, execute as seguintes ações:

- Certifique-se de que está a utilizar a versão mais recente do agente De reserva Azure.
- Tente criar manualmente um ponto de recuperação na área de tarefa de proteção.
- Certifique-se de que verifica a consistência da fonte de dados.
- Certifique-se de que está a cumprir os requisitos de conectividade da rede e largura de banda.
- Quando os dados da réplica estiverem num estado inconsistente, crie um ponto de recuperação do disco desta fonte de dados.
- Certifique-se de que a réplica está presente e não está em falta.
- Certifique-se de que a réplica tem espaço suficiente para criar o número de sequência de atualização (USN) de diário.

## <a name="error-unable-to-configure-protection"></a>Erro: Incapaz de configurar a proteção

Este erro ocorre quando o servidor do Gestor de Proteção de Dados não pode contactar o servidor protegido.

Para resolver esta questão, execute as seguintes ações:

- Certifique-se de que está a utilizar a versão mais recente do agente De reserva Azure.
- Certifique-se de que existe conectividade (rede/firewall/proxy) entre o servidor do Gestor de Proteção de Dados e o servidor protegido.
- Se estiver a proteger um servidor SQL, certifique-se de que a propriedade **Login Properties** > **NT AUTHORITY\SYSTEM** mostra a definição de **sysadmina** ativada.

## <a name="error-server-not-registered-as-specified-in-vault-credential-file"></a>Erro: Servidor não registado conforme especificado no ficheiro credencial do cofre

Este erro ocorre durante o processo de recuperação dos dados do gestor de proteção de dados/servidor de backup Azure. O ficheiro credencial do cofre que é usado no processo de recuperação não pertence ao cofre dos Serviços de Recuperação para o Gestor de Proteção de Dados/Servidor de Backup Azure.

Para resolver esta questão, execute estes passos:

1. Descarregue o ficheiro credencial do cofre do cofre dos Serviços de Recuperação para o qual o servidor de backup do Gestor de Proteção de Dados/Azure está registado.
2. Tente registar o servidor com o cofre usando o ficheiro credencial de cofre mais recentemente descarregado.

## <a name="error-no-recoverable-data-or-selected-server-not-a-data-protection-manager-server"></a>Erro: Sem dados recuperáveis ou servidor selecionado, não um servidor do Gestor de Proteção de Dados

Este erro ocorre pelas seguintes razões:

- Nenhum outro gestor de proteção de dados/servidores de backup Azure está registado no cofre dos Serviços de Recuperação.
- Os servidores ainda não enviaram os metadados.
- O servidor selecionado não é um gestor de proteção de dados/servidor de backup Azure.

Quando outros servidores de backup do Gestor de Proteção de Dados/Azure estiverem registados no cofre dos Serviços de Recuperação, execute estas etapas para resolver o problema:

1. Certifique-se de que o mais recente agente de backup Azure está instalado.
2. Depois de garantir que o mais recente agente está instalado, espere um dia antes de iniciar o processo de recuperação. O trabalho de reserva noturno envia os metadados para todas as cópias de segurança protegidas para a nuvem. Os dados de backup estão então disponíveis para recuperação.

## <a name="error-provided-encryption-passphrase-doesnt-match-passphrase-for-server"></a>Erro: Fornecido a frase de encriptação não corresponde à frase de passe do servidor

Este erro ocorre durante o processo de encriptação ao recuperar dados do gestor de proteção de dados/servidor de backup Azure. A frase-passe de encriptação que é usada no processo de recuperação não corresponde à frase de encriptação do servidor. Como resultado, o agente não pode desencriptar os dados e a recuperação falha.

> [!IMPORTANT]
> Se esquecer ou perder a frase de encriptação, não existem outros métodos para recuperar os dados. A única opção é regenerar a frase-passe. Utilize a nova palavra-passe para encriptar futuros dados de cópia de segurança.
>
> Quando estiver a recuperar dados, forneça sempre a mesma frase de encriptação que está associada ao servidor de backup Do Gestor de Proteção de Dados/Azure.
>
