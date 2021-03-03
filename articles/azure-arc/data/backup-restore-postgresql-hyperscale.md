---
title: Cópia de Segurança e Restauro para grupos de servidores da Base de Dados do Azure para PostgreSQL Hyperscale
description: Cópia de Segurança e Restauro para grupos de servidores da Base de Dados do Azure para PostgreSQL Hyperscale
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 12/09/2020
ms.topic: how-to
ms.openlocfilehash: 8b3304c673e8606667246a7d0df9ad8f3be11d9b
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101686704"
---
# <a name="back-up-and-restore-azure-arc-enabled-postgresql-hyperscale-server-groups"></a>Recue e restaure O Arco Azure ativou grupos de servidores de hiperescala pós-SQL

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

Quando faz o back up ou restaura o grupo de servidores pós-escala PostgreSQL ativado, todo o conjunto de bases de dados em todos os nós PostgreSQL do seu grupo de servidores é apoiado e/ou restaurado.

## <a name="take-a-manual-full-backup"></a>Pegue uma cópia de segurança manual

Para obter uma cópia de segurança completa de todos os dados e pastas de registo do seu grupo de servidor, execute o seguinte comando:
```console
azdata arc postgres backup create [--name <backup name>] --server-name <server group name> [--no-wait] 
```
Em que:
- __nome__ indica o nome de uma cópia de segurança
- __o nome do servidor__ indica um grupo de servidor
- __não esperar__ indica que a linha de comando não esperará que a cópia de segurança esteja completa para que possa continuar a usar esta janela de linha de comando

Este comando coordenará uma cópia de segurança completa distribuída em todos os nós que constituem o seu grupo de servidores pós-escala pós-SQL ativado pelo Arco Azure. Por outras palavras, irá fazer backup de todos os dados nos seus nós coordenador e operário.

Por exemplo:

```console
azdata arc postgres backup create --name backup12082020-0250pm --server-name postgres01
```

Quando a cópia de segurança terminar, o ID, nome, tamanho, estado e marcação de tempo da cópia de segurança serão devolvidos. Por exemplo:
```console
{
  "ID": "8085723fcbae4aafb24798c1458f4bb7",
  "name": "backup12082020-0250pm",
  "size": "9.04 MiB",
  "state": "Done",
  "timestamp": "2020-12-08 22:50:22+00:00"
}
```
`+xx:yy` indica o intervalo de tempo para o momento em que a cópia de segurança foi tomada. Neste exemplo, "+00:00" significa tempo UTC (UTC + 00 horas 00 minutos).

> [!NOTE]
> Ainda não é possível:
> - Agendar backups automáticos
> - Mostre o progresso de um backup enquanto está sendo tomada

## <a name="list-backups"></a>Listar cópias de segurança

Para listar as cópias de segurança disponíveis para restaurar, execute o seguinte comando:

```console
azdata arc postgres backup list --server-name <servergroup name>
```

Por exemplo:

```console
azdata arc postgres backup list --server-name postgres01
```

Devolve uma saída como:

```output
ID                                Name                   Size       State    Timestamp
--------------------------------  ---------------------  ---------  -------  -------------------------
d744303b1b224ef48be9cba4f58c7cb9  backup12072020-0731pm  13.83 MiB  Done     2020-12-08 03:32:09+00:00
c4f964d28da34318a420e6d14374bd36  backup12072020-0819pm  9.04 MiB   Done     2020-12-08 04:19:49+00:00
a304c6ef99694645a2a90ce339e94714  backup12072020-0822pm  9.1 MiB    Done     2020-12-08 04:22:26+00:00
47d1f57ec9014328abb0d8fe56020760  backup12072020-0827pm  9.06 MiB   Done     2020-12-08 04:27:22+00:00
8085723fcbae4aafb24798c1458f4bb7  backup12082020-0250pm  9.04 MiB   Done     2020-12-08 22:50:22+00:00
```

A coluna Timestamp indica o ponto no tempo utc no qual a cópia de segurança foi tomada.

## <a name="restore-a-backup"></a>Restaurar uma cópia de segurança
Nesta secção estamos a mostrar-lhe como fazer uma restauração completa ou um ponto de restauração no tempo. Quando restaurar uma cópia de segurança completa, restaure todo o conteúdo da cópia de segurança. Quando se faz um ponto de restauração no tempo, restaura-se até ao ponto indicado. Qualquer transação que tenha sido feita mais tarde do que este ponto no tempo não é restaurada.

### <a name="restore-a-full-backup"></a>Restaurar uma cópia de segurança completa
Para restaurar todo o conteúdo de uma cópia de segurança, executar o comando:
```console
azdata arc postgres backup restore --server-name <target server group name> [--source-server-name <source server group name> --backup-id <backup id>]
or
azdata arc postgres backup restore -sn <target server group name> [-ssn <source server group name> --backup-id <backup id>]
```
<!--To read the general format of restore command, run: azdata arc postgres backup restore --help -->

Em que:
- __backup-id__ é o ID da cópia de segurança mostrada no comando de backup da lista mostrado acima.
Isto coordenará uma restauração completa distribuída em todos os nós que constituem o seu grupo de servidores pós-escala pós-SQL ativado pelo Arco Azure. Por outras palavras, irá restaurar todos os dados nos seus nós coordenador e operário.

#### <a name="examples"></a>Exemplos:

__Restaurar o grupo de servidor postgres01 sobre si mesmo:__

```console
azdata arc postgres backup restore -sn postgres01 --backup-id d134f51aa87f4044b5fb07cf95cf797f
```

Esta operação é suportada apenas para a versão 12 do PostgreSQL.

__Restaurar o grupo de servidor postgres01 para um grupo de servidor diferente postgres02:__

```console
azdata arc postgres backup restore -sn postgres02 -ssn postgres01 --backup-id d134f51aa87f4044b5fb07cf95cf797f
```
Esta operação é suportada para qualquer versão da versão inicial do PostgreSQL. O grupo de servidor-alvo deve ser criado antes da operação de restauro, deve ter a mesma configuração e deve estar a utilizar o mesmo PVC de reserva que o grupo de servidor de origem.

Quando a operação de restauro estiver concluída, devolverá uma saída destas à linha de comando:

```json
{
  "ID": "d134f51aa87f4044b5fb07cf95cf797f",
  "state": "Done"
}
```

> [!NOTE]
> Ainda não é possível:
> - Restaurar uma cópia de segurança indicando o seu nome
> - Mostrar o progresso de uma operação de restauro


### <a name="do-a-point-in-time-restore"></a>Faça um ponto no tempo restaurar

Para restaurar um grupo de servidor até um ponto específico, executar o comando:
```console
azdata arc postgres backup restore --server-name <target server group name> --source-server-name <source server group name> --time <point in time to restore to>
or
azdata arc postgres backup restore -sn <target server group name> -ssn <source server group name> -t <point in time to restore to>
```

Para ler o formato geral do comando de restauro, corra: `azdata arc postgres backup restore --help` .

Onde `time` está o ponto no tempo para restaurar. Forneça um tempotamp ou um número e sufixo `m` (por minutos, `h` durante horas, `d` durante dias, ou durante `w` semanas). Por `1.5h` exemplo, remonta a 90 minutos.

#### <a name="examples"></a>Exemplos:
__Faça um ponto no tempo restaurar o grupo de servidor postgres01 sobre si mesmo:__

Ainda não é possível fazer ponto no tempo restaurar um grupo de servidor sobre si mesmo.

__Faça um ponto no tempo restaurar o grupo de servidor postgres01 para um grupo de servidor diferente postgres02 para uma placa de tempo específica:__
```console
azdata arc postgres backup restore -sn postgres02 -ssn postgres01 -t "2020-12-08 04:23:48.751326+00"
``` 

Este exemplo restaura no grupo de servidores postgres02 o estado em que o grupo de servidores postgres01 foi em 8 de dezembro de 2020 às 04:23:48.75 UTC. Note que "+00" indica o intervalo de tempo do ponto no tempo indicado. Se não indicar um fuso horário, será utilizado o fuso horário do cliente a partir do qual executou a operação de restauro.

Por exemplo:
- `2020-12-08 04:23:48.751326+00` é interpretado como `2020-12-08 04:23:48.751326` UTC
- se estiver no fuso horário padrão do Pacífico (PST = UTC+08), `2020-12-08 04:23:48.751326` é interpretado como `2020-12-08 12:23:48.751326` UTC Esta operação é suportada para qualquer versão da versão inicial do PostgreSQL. O grupo de servidor-alvo deve ser criado antes da operação de restauro e deve estar a utilizar o mesmo PVC de reserva que o grupo de servidor de origem.


__Faça um ponto no tempo restaurar o grupo de servidor postgres01 para um grupo de servidor diferente postgres02 para uma quantidade específica de tempo no passado:__
```console
azdata arc postgres backup restore -sn postgres02 -ssn postgres01 -t "22m"
```

Este exemplo restaura no grupo de servidores postgres02 o estado em que o grupo de servidores postgres01 foi há 22 minutos.
Esta operação é suportada para qualquer versão da versão inicial do PostgreSQL. O grupo de servidor-alvo deve ser criado antes da operação de restauro e deve estar a utilizar o mesmo PVC de reserva que o grupo de servidor de origem.

> [!NOTE]
> Ainda não é possível:
> - Mostrar o progresso de uma operação de restauro

## <a name="delete-backups"></a>Eliminar cópias de segurança

A retenção de backup não pode ser definida na pré-visualização. No entanto, pode eliminar manualmente cópias de segurança de que não necessita.
O comando geral para eliminar cópias de segurança é:

```console
azdata arc postgres backup delete  [--server-name, -sn] {[--name, -n], -id}
```

em que:
- `--server-name` é o nome do grupo de servidor a partir do qual o utilizador quer eliminar uma cópia de segurança
- `--name` é o nome da cópia de segurança para apagar
- `-id`é o ID da cópia de segurança para eliminar

> [!NOTE]
> `--name` e `-id` são mutuamente exclusivos.

Por exemplo:

```console
azdata arc postgres backup delete -sn postgres01 -n MyBackup091720200110am
{
  "ID": "5b0481dfc1c94b4cac79dd56a1bb21f4",
  "name": "MyBackup091720200110am",
  "state": "Done"
}
```

Pode recuperar o nome e o ID das suas cópias de segurança executando o comando de backup da lista, conforme explicado no parágrafo anterior.

Para mais detalhes sobre o comando de eliminação, corra:

```console
azdata arc postgres backup delete --help
```

## <a name="next-steps"></a>Passos seguintes
- Leia sobre [a escala (adicionar nós de trabalhador)](scale-out-postgresql-hyperscale-server-group.md) o seu grupo de servidor
- Leia sobre [o escalonamento para cima ou para baixo (aumentando/diminuindo a memória/vcores)](scale-up-down-postgresql-hyperscale-server-group-using-cli.md) o seu grupo de servidor
