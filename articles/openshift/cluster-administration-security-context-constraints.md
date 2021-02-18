---
title: Gerir constrangimentos de contexto de segurança em Azure Red Hat OpenShift | Microsoft Docs
description: Restrições de contexto de segurança para administradores de cluster Azure Red Hat OpenShift
services: container-service
author: troy0820
ms.author: b-trconn
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 09/25/2019
ms.openlocfilehash: 977504c1faec9bd8134646a8cbe31f9eea665edd
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100636208"
---
# <a name="manage-security-context-constraints-in-azure-red-hat-openshift"></a>Gerir constrangimentos de contexto de segurança no Azure Red Hat OpenShift

> [!IMPORTANT]
> Azure Red Hat OpenShift 3.11 será retirado a 30 de junho de 2022. O apoio à criação de novos clusters Azure Red Hat OpenShift 3.11 continua até 30 de novembro de 2020. Após a reforma, os restantes clusters Azure Red Hat OpenShift 3.11 serão encerrados para evitar vulnerabilidades de segurança.
> 
> Siga este guia para [criar um cluster Azure Red Hat OpenShift 4](tutorial-create-cluster.md).
> Se tiver questões específicas, [contacte-nos.](mailto:arofeedback@microsoft.com)

As restrições de contexto de segurança (SCCs) permitem que os administradores de cluster controlem permissões para cápsulas. Para saber mais sobre este tipo de API, consulte a documentação de [arquitetura para SCCs.](https://docs.openshift.com/container-platform/3.11/architecture/additional_concepts/authorization.html) Pode gerir os SCCs no seu caso como objetos API normais utilizando o CLI.

## <a name="list-security-context-constraints"></a>Listar constrangimentos de contexto de segurança

Para obter uma lista atual de SCCs, use este comando: 

```bash
$ oc get scc

NAME               PRIV      CAPS      SELINUX     RUNASUSER          FSGROUP     SUPGROUP    PRIORITY   READONLYROOTFS   VOLUMES
anyuid             false     []        MustRunAs   RunAsAny           RunAsAny    RunAsAny    10         false            [configMap downwardAPI emptyDir persistentVolumeClaim secret]
hostaccess         false     []        MustRunAs   MustRunAsRange     MustRunAs   RunAsAny    <none>     false            [configMap downwardAPI emptyDir hostPath persistentVolumeClaim secret]
hostmount-anyuid   false     []        MustRunAs   RunAsAny           RunAsAny    RunAsAny    <none>     false            [configMap downwardAPI emptyDir hostPath nfs persistentVolumeClaim secret]
hostnetwork        false     []        MustRunAs   MustRunAsRange     MustRunAs   MustRunAs   <none>     false            [configMap downwardAPI emptyDir persistentVolumeClaim secret]
nonroot            false     []        MustRunAs   MustRunAsNonRoot   RunAsAny    RunAsAny    <none>     false            [configMap downwardAPI emptyDir persistentVolumeClaim secret]
privileged         true      [*]       RunAsAny    RunAsAny           RunAsAny    RunAsAny    <none>     false            [*]
restricted         false     []        MustRunAs   MustRunAsRange     MustRunAs   RunAsAny    <none>     false            [configMap downwardAPI emptyDir persistentVolumeClaim secret]
```

## <a name="examine-an-object-for-security-context-constraints"></a>Examine um objeto para restrições de contexto de segurança

Para examinar um SCC em particular, `oc get` `oc describe` use, ou `oc edit` .  Por exemplo, para examinar o SCC **restrito,** utilize este comando:
```bash
$ oc describe scc restricted
Name:                    restricted
Priority:                <none>
Access:
  Users:                <none>
  Groups:                system:authenticated
Settings:
  Allow Privileged:            false
  Default Add Capabilities:        <none>
  Required Drop Capabilities:        KILL,MKNOD,SYS_CHROOT,SETUID,SETGID
  Allowed Capabilities:            <none>
  Allowed Seccomp Profiles:        <none>
  Allowed Volume Types:            configMap,downwardAPI,emptyDir,persistentVolumeClaim,projected,secret
  Allow Host Network:            false
  Allow Host Ports:            false
  Allow Host PID:            false
  Allow Host IPC:            false
  Read Only Root Filesystem:        false
  Run As User Strategy: MustRunAsRange
    UID:                <none>
    UID Range Min:            <none>
    UID Range Max:            <none>
  SELinux Context Strategy: MustRunAs
    User:                <none>
    Role:                <none>
    Type:                <none>
    Level:                <none>
  FSGroup Strategy: MustRunAs
    Ranges:                <none>
  Supplemental Groups Strategy: RunAsAny
    Ranges:                <none>
```
## <a name="next-steps"></a>Passos seguintes
> [!div class="nextstepaction"]
> [Criar um cluster do Azure Red Hat OpenShift](tutorial-create-cluster.md) 
