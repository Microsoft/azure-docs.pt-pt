---
title: Gerir o acesso sSH a contas de domínio no Azure HDInsight
description: Passos para gerir o acesso sSH para contas Azure AD no HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 5529989384df75b592afa8f5e4960eb9817fb2d7
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77472521"
---
# <a name="manage-ssh-access-for-domain-accounts-in-azure-hdinsight"></a>Gerir o acesso sSH a contas de domínio no Azure HDInsight

Em clusters seguros, por padrão, todos os utilizadores de domínio em [DS AD Azure](../../active-directory-domain-services/overview.md) são autorizados a [SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) nos nós da cabeça e borda. Estes utilizadores não fazem parte do grupo sudoers e não têm acesso à raiz. O utilizador SSH criado durante a criação do cluster terá acesso raiz.

## <a name="manage-access"></a>Gerir o acesso

Para modificar o acesso sSH a utilizadores ou grupos específicos, atualize `/etc/ssh/sshd_config` em cada um dos nós.

1. Utilize [o comando ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) para se ligar ao seu cluster. Editar o comando abaixo substituindo CLUSTERNAME pelo nome do seu cluster e, em seguida, introduzir o comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Abra o ficheiro `ssh_confi`g.

    ```bash
    sudo nano /etc/ssh/sshd_config
    ```

1. Modificar o ficheiro `sshd_config` conforme desejado. Se restringir os utilizadores a determinados grupos, então as contas locais não podem entrar nesse nó. Segue-se apenas um exemplo de sintaxe:

    ```bash
    AllowUsers useralias1 useralias2

    AllowGroups groupname1 groupname2
    ```

    Em seguida, guardar alterações: **CTRL + X,** **Y,** **Enter**.

1. Reinicie o sshd.

    ```bash
    sudo systemctl restart sshd
    ```

1. Repita acima dos passos para cada nó.

## <a name="ssh-authentication-log"></a>Registo de autenticação SSH

O registo de autenticação SSH está escrito em `/var/log/auth.log`. Se vir alguma falha de login através do SSH para contas locais ou de domínio, terá de passar pelo registo para desinviar os erros. Muitas vezes, o problema pode estar relacionado com contas específicas do utilizador e é geralmente uma boa prática experimentar outras contas de utilizador ou SSH usando o utilizador sSH padrão (conta local) e, em seguida, tentar um kinit.

## <a name="ssh-debug-log"></a>Registo de depuração SSH

Para permitir a exploração madeireira verbosa, terá de reiniciar `sshd` com a opção `-d`. Como `/usr/sbin/sshd -d` Você também pode correr `sshd` em uma porta personalizada (como 2222) para que você não tenha que parar o daemon principal SSH. Também pode utilizar `-v` opção com o cliente SSH para obter mais registos (visão do lado do cliente das falhas).

## <a name="next-steps"></a>Passos seguintes

* [Gerir clusters HDInsight com pacote de segurança empresarial](./apache-domain-joined-manage.md)
* [Ligue-se ao HDInsight (Apache Hadoop) utilizando SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).
