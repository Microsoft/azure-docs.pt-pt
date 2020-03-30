---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: cb160a140b5c0cb184a5172da10ade0de37c4fed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "67184303"
---
<!--v-sharos 10/13/2105 virtual device security-->

Tenha em mente as seguintes considerações de segurança quando utilizar o dispositivo virtual StorSimple:

* O dispositivo virtual é protegido através da subscrição do Microsoft Azure. Isto significa que se estiver a utilizar o dispositivo virtual e a sua subscrição Azure estiver comprometida, os dados armazenados no seu dispositivo virtual também são suscetíveis.
* A chave pública do certificado utilizado para encriptar dados armazenados no Azure StorSimple é disponibilizada de forma segura para o portal clássico Azure, e a chave privada é mantida com o dispositivo StorSimple. No dispositivo virtual StorSimple, tanto as chaves públicas como privadas são armazenadas em Azure.
* O dispositivo virtual está hospedado no centro de dados do Microsoft Azure.

