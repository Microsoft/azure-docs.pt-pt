---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: cb160a140b5c0cb184a5172da10ade0de37c4fed
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "67184303"
---
<!--v-sharos 10/13/2105 virtual device security-->

Tenha em mente as seguintes considerações de segurança quando utilizar o dispositivo virtual StorSimple:

* O dispositivo virtual é protegido através da subscrição do Microsoft Azure. Isto significa que se estiver a utilizar o dispositivo virtual e a subscrição do Azure estiver comprometida, os dados armazenados no seu dispositivo virtual também são suscetíveis.
* A chave pública do certificado utilizado para encriptar os dados armazenados no Azure StorSimple é seguramente disponibilizada ao portal clássico Azure, e a chave privada é mantida com o dispositivo StorSimple. No dispositivo virtual StorSimple, as chaves públicas e privadas são armazenadas em Azure.
* O dispositivo virtual encontra-se hospedado no datacenter do Microsoft Azure.

