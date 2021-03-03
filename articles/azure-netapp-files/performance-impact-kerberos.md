---
title: Impacto de desempenho da Kerberos nos volumes NFSv4.1 dos ficheiros Azure NetApp | Microsoft Docs
description: Descreve as opções de segurança disponíveis, os vetores de desempenho testados e o impacto de desempenho esperado dos kerberos nos volumes NFSv4.1 dos ficheiros Azure NetApp.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: b-juche
ms.openlocfilehash: e9054f11c8f55e9fe00266840a9f6e257f14e659
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101747209"
---
# <a name="performance-impact-of-kerberos-on-azure-netapp-files-nfsv41-volumes"></a>Impacto de desempenho de Kerberos nos volumes NFSv4.1 dos ficheiros Azure NetApp

O Azure NetApp Files suporta [a encriptação do cliente NFS nos](configure-kerberos-encryption.md) modos Kerberos (krb5, krb5i e krb5p) com encriptação AES-256. Este artigo descreve o impacto de desempenho de Kerberos nos volumes NFSv4.1. 

## <a name="available-security-options"></a>Opções de segurança disponíveis 

As opções de segurança atualmente disponíveis para volumes NFSv4.1 são as seguintes: 

* **sec=sys** usa UIDs e GIDs locais uids usando AUTH_SYS para autenticar operações NFS.
* **sec=krb5** utiliza Kerberos V5 em vez de UIDs e GIDs locais unix para autenticar os utilizadores.
* **sec=krb5i** utiliza Kerberos V5 para autenticação do utilizador e realiza verificação de integridade das operações NFS utilizando despesas de verificação seguras para evitar a adulteração de dados.
* **sec=krb5p** utiliza Kerberos V5 para verificação de autenticação e integridade do utilizador. Encripta o tráfego da NFS para evitar o cheiro de tráfego. Esta opção é a configuração mais segura, mas também envolve a maior sobrecarga de desempenho.

## <a name="performance-vectors-tested"></a>Vetores de desempenho testados

Esta secção descreve o impacto de desempenho do lado único do cliente das várias `sec=*` opções.

* O impacto do desempenho foi testado em dois níveis: baixa conusão (baixa carga) e elevada concordância (limites superiores de I/O e produção).  
* Foram testados três tipos de cargas de trabalho:  
    * Pequena operação leitura/escrita aleatória (usando FIO)
    * Leitura/escrita sequencial de grande operação (utilizando o FIO)
    * Metadados pesados de carga de trabalho gerados por aplicações como git

## <a name="expected-performance-impact"></a>Impacto de desempenho esperado 

Existem duas áreas de foco: carga leve e limite superior. As listas que se seguem descrevem a definição de segurança do impacto de desempenho por definição de segurança e cenário por cenário. Todas as comparações são feitas com o `sec=sys` parâmetro de segurança. O teste foi feito num único volume, usando um único cliente. 

Impacto de desempenho do krb5:

* Baixa concordância (r/w):
    * A latência sequencial aumentou 0,3 ms.
    * A latência aleatória de I/O aumentou 0,2 ms.
    * A latência dos metadados I/O aumentou 0,2 ms.
* Alta concordância (r/w): 
    * A produção sequencial máxima foi desacompanhada por krb5.
    * A E/S aleatória máxima diminuiu 30% para cargas de trabalho de leitura pura, com o impacto global a cair para zero à medida que a carga de trabalho muda para a escrita pura. 
    * A carga máxima de metadados diminuiu 30%.

Impacto de desempenho do krb5i: 

* Baixa concordância (r/w):
    * A latência sequencial aumentou 0,5 ms.
    * A latência aleatória de I/O aumentou 0,2 ms.
    * A latência dos metadados I/O aumentou 0,2 ms.
* Alta concordância (r/w): 
    * A produção sequencial máxima diminuiu 70% em termos globais, independentemente da mistura de carga de trabalho.
    * A E/S aleatória máxima diminuiu 50% para cargas de trabalho de leitura pura, com o impacto global a diminuir para 25% à medida que a carga de trabalho passa para a escrita pura. 
    * A carga máxima de metadados diminuiu 30%.

Impacto de desempenho do krb5p:

* Baixa concordância (r/w):
    * A latência sequencial aumentou 0,8 ms.
    * A latência aleatória de I/O aumentou 0,2 ms.
    * A latência dos metadados I/O aumentou 0,2 ms.
* Alta concordância (r/w): 
    * A produção sequencial máxima diminuiu 85% em termos globais, independentemente da mistura de carga de trabalho. 
    * A E/S aleatória máxima diminuiu 65% para cargas de trabalho de leitura pura, com o impacto global a diminuir para 43% à medida que a carga de trabalho passa para a escrita pura. 
    * A carga máxima de metadados diminuiu 30%.

## <a name="next-steps"></a>Passos seguintes  

* [Configurar a encriptação NFSv4.1 Kerberos para o Azure NetApp Files](configure-kerberos-encryption.md) 
