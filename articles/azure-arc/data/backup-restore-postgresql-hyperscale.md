---
title: Cópia de Segurança e Restauro para grupos de servidores da Base de Dados do Azure para PostgreSQL Hyperscale
description: Cópia de Segurança e Restauro para grupos de servidores da Base de Dados do Azure para PostgreSQL Hyperscale
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 4fb64a2ea55744d66b203ef4d901f22ae4695e1a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91630428"
---
# <a name="backup-and-restore-for-azure-arc-enabled-postgresql-hyperscale-server-groups"></a>Backup e restauro para Azure Arc ativado grupos de servidores de hiperescala postgreSQL

Pode fazer cópia de segurança/restauro completo do grupo de servidores pós-escala pós-escala Azure Arc. Quando o fizer, todo o conjunto de bases de dados em todos os nós do seu grupo de servidores de hiperescala pós-escala ativado por Azure Arc é apoiado e/ou restaurado.
Para fazer uma cópia de segurança e restaurá-la, tem de se certificar de que uma classe de armazenamento de backup está configurada para o seu grupo de servidor. Por enquanto, tem de indicar uma classe de armazenamento de cópias de segurança no momento em que criar o grupo de servidor. Ainda não é possível configurar o seu grupo de servidor para utilizar uma classe de armazenamento de backup depois de ter sido criado.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

> [!CAUTION]
> A pré-visualização não suporta cópia de segurança/restauro para a versão 11 do motor Postgres. Só suporta backup/restauro para a versão 12 do Postgres.

## <a name="verify-configuration"></a>Verificar configuração

Em primeiro lugar, verifique se o seu grupo de servidor existente foi configurado para utilizar a classe de armazenamento de backup.

Executar o seguinte comando depois de definir o nome do seu grupo de servidor:
```console
 azdata arc postgres server show -n postgres01
```
Veja a secção de armazenamento da saída:
```console
...
"storage": {
      "backups": {
        "className": "local-storage"
      },
      "data": {
        "className": "local-storage",
        "size": "5Gi"
      },
      "logs": {
        "className": "local-storage",
        "size": "5Gi"
      }
    }
...
```
Se vir o nome de uma classe de armazenamento indicada na secção "backups" da saída desse comando, significa que o seu grupo de servidor foi configurado para utilizar uma classe de armazenamento de backup e está pronto para fazer cópias de segurança e fazer restauros. Se não vir uma secção de "backups", tem de eliminar e recriar o seu grupo de servidor para configurar a classe de armazenamento de backup. Neste momento, ainda não é possível configurar uma classe de armazenamento de backup após a criação do grupo de servidor.

>[!IMPORTANT]
>Se o seu grupo de servidor já estiver configurado para utilizar uma classe de armazenamento de backup, salte o passo seguinte e vá diretamente para o passo "Leve a cópia de segurança manual".

## <a name="create-a-server-group"></a>Criar um grupo de servidores 

Em seguida, crie um grupo de servidor configurado para cópia de segurança/restauro.

Para poder pegar em backups e restaurá-los, é necessário criar um servidor configurado com uma classe de armazenamento.

Para obter uma lista das aulas de armazenamento disponíveis no seu cluster Kubernetes, execute o seguinte comando:

```console
kubectl get sc
```

<!--The general format of create server group command is documented [here](create-postgresql-instances.md)-->

```console
azdata arc postgres server create -n <name> --workers 2 --storage-class-backups <storage class name> [--storage-class-data <storage class name>] [--storage-class-logs <storage class name>]
```

Por exemplo, se criou um ambiente simples baseado no kubeadm:
```console
azdata arc postgres server create -n postgres01 --workers 2 --storage-class-backups local-storage
```

## <a name="take-manual-full-backup"></a>Pegue o backup manual completo

Em seguida, pegue uma cópia de segurança manual.

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
azdata arc postgres backup create --name MyBackup_Aug31_0730amPST --server-name postgres01
```

Quando a cópia de segurança terminar, a identificação, nome e estado da cópia de segurança serão devolvidos. Por exemplo:
```console
{
  "ID": "d134f51aa87f4044b5fb07cf95cf797f",
  "name": "MyBackup_Aug31_0730amPS",
  "state": "Done"
}
```

> [!NOTE]
> Ainda não é possível:
> - Agendar backups automáticos
> - Mostre o progresso de um backup enquanto está sendo tomada

## <a name="list-backups"></a>Listar cópias de segurança

Enuma as cópias de segurança que estão disponíveis para restaurar.

Para listar as cópias de segurança disponíveis para restaurar, execute o seguinte comando:

```console
azdata arc postgres backup list --server-name <servergroup name>
```

Por exemplo:
```console
azdata arc postgres backup list --server-name postgres01
```

Devolverá uma saída como:
```console
ID                                Name                      State    Timestamp
--------------------------------  ------------------------  -------  ------------------------------
d134f51aa87f4044b5fb07cf95cf797f  MyBackup_Aug31_0730amPST  Done     2020-08-31 14:30:00:00+00:00
```

O calendário indica o ponto no tempo utc no qual a cópia de segurança foi tomada.

## <a name="restore-a-backup"></a>Restaurar uma cópia de segurança

Para restaurar a cópia de segurança de todo um grupo de servidores, executar o comando:

```console
azdata arc postgres backup restore --server-name <server group name> --backup-id <backup id>
```

Em que:
- __backup-id__ é o ID da cópia de segurança mostrada no comando de backup da lista (consulte o Passo 3).
Isto coordenará uma restauração completa distribuída em todos os nós que constituem o seu grupo de servidores pós-escala pós-SQL ativado pelo Arco Azure. Por outras palavras, irá restaurar todos os dados nos seus nós coordenador e operário.

Por exemplo:
```console
azdata arc postgres backup restore --server-name postgres01 --backup-id d134f51aa87f4044b5fb07cf95cf797f
```

Quando a operação de restauro estiver concluída, devolverá uma saída destas à linha de comando:
```console
{
  "ID": "d134f51aa87f4044b5fb07cf95cf797f",
  "state": "Done"
}
```
> [!NOTE]
> Ainda não é possível:
> - Restaurar uma cópia de segurança indicando o seu nome
> - Restaurar um grupo de servidor sob um nome diferente ou em um grupo de servidor diferente
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

Pode recuperar o nome e o ID das suas cópias de segurança executando o comando de backup da lista, conforme explicado no parágrafo anterior.

Por exemplo, considere que tem as seguintes cópias de segurança listadas:
```console
azdata arc postgres backup list -sn postgres01
ID                                Name                    State
--------------------------------  ----------------------  -------
5b0481dfc1c94b4cac79dd56a1bb21f4  MyBackup091720200110am  Done
0cf39f1e92344e6db4cfa285d36c7b14  MyBackup091720200111am  Done
```
e se quiser apagar o primeiro deles, executará o seguinte comando:
```console
azdata arc postgres backup delete -sn postgres01 -n MyBackup091720200110am
{
  "ID": "5b0481dfc1c94b4cac79dd56a1bb21f4",
  "name": "MyBackup091720200110am",
  "state": "Done"
}
```
Se enumerasse os backups nessa altura, obteria a seguinte saída:
```console
azdata arc postgres backup list -sn postgres01
ID                                Name                    State
--------------------------------  ----------------------  -------
0cf39f1e92344e6db4cfa285d36c7b14  MyBackup091720200111am  Done
```

Para mais detalhes sobre o comando de eliminação, corra:
```console
azdata arc postgres backup delete --help
```

## <a name="next-steps"></a>Passos seguintes
- Leia sobre [a escala (adicionar nós de trabalhador)](scale-out-postgresql-hyperscale-server-group.md) o seu grupo de servidor
- Leia sobre [o escalonamento para cima ou para baixo (aumentando/diminuindo a memória/vcores)](scale-up-down-postgresql-hyperscale-server-group-using-cli.md) o seu grupo de servidor
