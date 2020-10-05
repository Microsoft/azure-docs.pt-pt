---
title: Exemplos de uma política de Azure Attestation
description: Exemplos da política de Azure Attestation.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: 537f02b26c4ea3be59097a120482f98109f8b16a
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2020
ms.locfileid: "89237423"
---
# <a name="examples-of-an-attestation-policy"></a>Exemplos de uma política de atestado

A política de atestado é usada para processar as provas de atestado e determinar se o Azure Attestation emitirá um token de atestado. A geração de token attestation pode ser controlada com políticas personalizadas. Seguem-se alguns exemplos de uma política de atestado.

## <a name="default-policy-for-an-sgx-enclave-with-policyformattext"></a>Política padrão para um enclave SGX com PolicyFormat=Text

```
Version= 1.0;
authorizationrules
{
    c:[type==”$is-debuggable”] => permit();
};
issuancerules
{
    c:[type==”$is-debuggable”] => issue(type=”is-debuggable”, value=c.value);
    c:[type==”$sgx-mrsigner”] => issue(type=”sgx-mrsigner”, value=c.value);
    c:[type==”$sgx-mrenclave”] => issue(type=”sgx-mrenclave”, value=c.value);
    c:[type==”$product-id”] => issue(type=”product-id”, value=c.value);
    c:[type==”$svn”] => issue(type=”svn”, value=c.value);
    c:[type==”$tee”] => issue(type=”tee”, value=c.value);
};
```

## <a name="default-policy-for-vbs-enclave"></a>Política padrão para o enclave VBS

Não há política padrão para o enclave VBS


## <a name="unsigned-policy-for-an-sgx-enclave-with-policyformatjwt"></a>Política não assinada para um enclave SGX com PolicyFormat=JWT

```
eyJhbGciOiJub25lIn0.eyJBdHRlc3RhdGlvblBvbGljeSI6ICJkbVZ5YzJsdmJqMGdNUzR3TzJGMWRHaHZjbWw2WVhScGIyNXlkV3hsYzN0ak9sdDBlWEJsUFQwaUpHbHpMV1JsWW5WbloyRmliR1VpWFNBOVBpQndaWEp0YVhRb0tUdDlPMmx6YzNWaGJtTmxjblZzWlhON1l6cGJkSGx3WlQwOUlpUnBjeTFrWldKMVoyZGhZbXhsSWwwZ1BUNGdhWE56ZFdVb2RIbHdaVDBpYVhNdFpHVmlkV2RuWVdKc1pTSXNJSFpoYkhWbFBXTXVkbUZzZFdVcE8yTTZXM1I1Y0dVOVBTSWtjMmQ0TFcxeWMybG5ibVZ5SWwwZ1BUNGdhWE56ZFdVb2RIbHdaVDBpYzJkNExXMXljMmxuYm1WeUlpd2dkbUZzZFdVOVl5NTJZV3gxWlNrN1l6cGJkSGx3WlQwOUlpUnpaM2d0YlhKbGJtTnNZWFpsSWwwZ1BUNGdhWE56ZFdVb2RIbHdaVDBpYzJkNExXMXlaVzVqYkdGMlpTSXNJSFpoYkhWbFBXTXVkbUZzZFdVcE8yTTZXM1I1Y0dVOVBTSWtjSEp2WkhWamRDMXBaQ0pkSUQwLUlHbHpjM1ZsS0hSNWNHVTlJbkJ5YjJSMVkzUXRhV1FpTENCMllXeDFaVDFqTG5aaGJIVmxLVHRqT2x0MGVYQmxQVDBpSkhOMmJpSmRJRDAtSUdsemMzVmxLSFI1Y0dVOUluTjJiaUlzSUhaaGJIVmxQV011ZG1Gc2RXVXBPMk02VzNSNWNHVTlQU0lrZEdWbElsMGdQVDRnYVhOemRXVW9kSGx3WlQwaWRHVmxJaXdnZG1Gc2RXVTlZeTUyWVd4MVpTazdmVHMifQ.
```

## <a name="signed-policy-for-an-sgx-enclave-with-policyformatjwt"></a>Política assinada para um enclave SGX com PolicyFormat=JWT

```
eyAiYWxnIjoiUlMyNTYiLCAieDVjIjogWyJNSUlETERDQ0FoU2dBd0lCQWdJSVpTYW5zQ1djS1RNd0RRWUpLb1pJaHZjTkFRRUxCUUF3RnpFVk1CTUdBMVVFQXd3TVRXRmhWR1Z6ZEVObGNuUXhNQ0FYRFRJd01EUXlOVEF3TURBd01Gb1lEekl3TnpBd05ESTFNREF3TURBd1dqQVhNUlV3RXdZRFZRUUREQXhOWVdGVVpYTjBRMlZ5ZERFd2dnRWlNQTBHQ1NxR1NJYjNEUUVCQVFVQUE0SUJEd0F3Z2dFS0FvSUJBUUNjbFVEcGJnVDM3My9GZUZCS0lwZTFoL3k0dTM2Z09NSTJOcFZVS3pVZ2krdVp5U042dTE5OVlESEtwYVVUZE1iNzd6THdCRnJmdWx4SHo3aVkyTEFWTmo5R01kZXpIbGdrZDgyaTJ0N2Rmd3hkbG8xZTlrbGFhQmUrTEZWL1dITDJrN1JSeG5mRFU2YktrK3lkWWY4REtSRUdyZEc2bzJqRW1CQVBxREQzaSszNENVVjlyTnk2bW5VTGI1ZjFDZnI0eERZTEdUTDNyS0VDaU12SFAyVllnbTBneEpmdXlDR1laYkRmSWVtcTA3QmlMYmt4dm4xOG1qR0dzNHlCQ0ZLZmZrMG9Ya1FHMU9uRHpyWVdObGVtNW1mUE5DY1RqOUVUYzBqbEI3b2dMcVZWNUxxOW9pb0M1S3ErR3hLaWwxSk51UnQrZkxEZTFtZWlXWitldTg5N0FnTUJBQUdqZWpCNE1FWUdBMVVkSXdRL01EMkFGRWhERjBadDFqclNqQ0ZDVnZacG5YWTdvdk4xb1J1a0dUQVhNUlV3RXdZRFZRUUREQXhOWVdGVVpYTjBRMlZ5ZERHQ0NHVW1wN0FsbkNrek1CMEdBMVVkRGdRV0JCUklReGRHYmRZNjBvd2hRbGIyYVoxMk82THpkVEFQQmdOVkhSTUJBZjhFQlRBREFRSC9NQTBHQ1NxR1NJYjNEUUVCQ3dVQUE0SUJBUUE5MHJYSlY4WmpHcWRscWVZeEV3RTBoeENhdHBaY04yckdjUkMvVVJ0Um5KQlFiV3BCKzc5ZHpaSFhPK1VJRjd6aFZVd3czZVF5UXVhaDBhQzZzNnRCVUtqZ0Zqa0s5Wkw2U2MvNHFweWI2UkUxSGdqUkNjZWdtVSs4MENkY3F4Z29xWFRVeUZXeXFtQWxzSFh6VzN4aWpyTjFIOXpkdDdwdHNiQ1hPNHBiME5qcXo1NHpzS01RTDg0WlRNOWZYamt0N2FacEtuaGw1TlAzMTFTQ01VTzRrem1xaW0zMzFScFd2dXhabnQxZjFrbDRRZUxoL1lBRjcrT0VLTmFGZ3l4T1hGMkROdk1GRFlFV0V3NUY5dkRrMFZlc1VFck9nYTN2RmxJWTl5ZkU4aEY3OXFudHNNWVFuY0pBRklBNVJaZmZMVG45SGxZbVp0SmVPWmxDR3l6aSJdfQ.eyJBdHRlc3RhdGlvblBvbGljeSI6ICJ2ZXJzaW9uPSAxLjA7YXV0aG9yaXphdGlvbnJ1bGVze2M6W3R5cGU9PVwiJGlzLWRlYnVnZ2FibGVcIl0gPT4gcGVybWl0KCk7fTtpc3N1YW5jZXJ1bGVze2M6W3R5cGU9PVwiJGlzLWRlYnVnZ2FibGVcIl0gPT4gaXNzdWUodHlwZT1cImlzLWRlYnVnZ2FibGVcIiwgdmFsdWU9Yy52YWx1ZSk7YzpbdHlwZT09XCIkc2d4LW1yc2lnbmVyXCJdID0-IGlzc3VlKHR5cGU9XCJzZ3gtbXJzaWduZXJcIiwgdmFsdWU9Yy52YWx1ZSk7YzpbdHlwZT09XCIkc2d4LW1yZW5jbGF2ZVwiXSA9PiBpc3N1ZSh0eXBlPVwic2d4LW1yZW5jbGF2ZVwiLCB2YWx1ZT1jLnZhbHVlKTtjOlt0eXBlPT1cIiRwcm9kdWN0LWlkXCJdID0-IGlzc3VlKHR5cGU9XCJwcm9kdWN0LWlkXCIsIHZhbHVlPWMudmFsdWUpO2M6W3R5cGU9PVwiJHN2blwiXSA9PiBpc3N1ZSh0eXBlPVwic3ZuXCIsIHZhbHVlPWMudmFsdWUpO2M6W3R5cGU9PVwiJHRlZVwiXSA9PiBpc3N1ZSh0eXBlPVwidGVlXCIsIHZhbHVlPWMudmFsdWUpO2M6W3R5cGU9PVwiJHRlZS1mdXR1cmVcIl0gPT4gaXNzdWUodHlwZT1cInRlZS1mdXR1cmVcIiwgdmFsdWU9Yy52YWx1ZSk7fTsifQ.Rm_KOcxPzo_6AytCyoWrgRa50efT_87gR9_Yewxeuesji99drOepXp50qabnPc0oCyy5ArcPSETeYCaYQrrNVhCTEW-3U_WJxXKrHuQIa9IcE06muFrA4X4esjCitbgrTujKzf83puB5u8vUG0KI_y9FAKGIjrZzDRXAG0126DOCjMltUxyRpNpXq5Ex6SQI5q-CQctoO9kYkMrYoUTqAxiAJ8mpNvC4Ufe4TjF8t6SpSI2zXXta_gY_decJ8AkyDx4YkzbHn5QzjaXaXoHMgbj_76D_RnGhANC0u5rBHvqScFMTO50QnPW94frkn-9PBtWuQ0g7Q1TeERkoMknhBA       
```

</br>

## <a name="next-steps"></a>Passos seguintes

- [Como autor e assinar uma política de atestado](author-sign-policy.md)
- [Configurar a Azure Attestation usando o PowerShell](quickstart-powershell.md)
