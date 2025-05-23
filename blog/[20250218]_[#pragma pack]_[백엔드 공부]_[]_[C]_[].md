## **🔍 `#pragma pack(1)`의 의미와 사용법**
### **✅ `#pragma pack(1)`란?**
`#pragma pack(1)`은 **구조체(struct)의 메모리 정렬(Alignment)을 1바이트 단위로 설정**하여 **패딩(Padding)을 제거**하는 지시문입니다.  
즉, **구조체 내부의 모든 필드를 강제로 1바이트 크기로 정렬**하여 불필요한 메모리 낭비를 방지합니다.

### **🔹 예제: `#pragma pack(1)`을 적용한 구조체**
```c
#include <stdio.h>

#pragma pack(1)  // 1바이트 단위로 정렬
struct Test {
    char a;   // 1바이트
    int b;    // 4바이트
    char c;   // 1바이트
};

#pragma pack()  // 원래 정렬 방식으로 복구

int main() {
    printf("sizeof(struct Test): %zu\n", sizeof(struct Test));
    return 0;
}
```

**📌 예상 출력**
```
sizeof(struct Test): 6
```
- 기본적으로 **패딩이 없는 상태**가 됩니다.
- 만약 `#pragma pack(1)`을 **사용하지 않았다면**, `sizeof(struct Test)`는 **8바이트 또는 12바이트**가 될 수도 있습니다(컴파일러의 기본 정렬 규칙에 따라 다름).

---

## **🔍 `#pragma pack(1)`의 장단점**
### **✅ 장점**
| 장점 | 설명 |
|------|------|
| **메모리 절약** | 패딩을 제거하여 메모리 낭비를 줄일 수 있음. |
| **이진 파일 포맷과 호환** | 네트워크 전송, 파일 저장 시 패딩 없이 정확한 크기로 구조체를 저장 가능. |
| **하드웨어 메모리 맵과 일치 가능** | 하드웨어 레지스터 매핑 시 정확한 크기를 보장할 수 있음. |

**📌 예제: 파일 저장 시 크기 절약**
```c
struct Test {
    char a;   // 1바이트
    int b;    // 4바이트
    char c;   // 1바이트
};
```
- 일반적으로 `sizeof(struct Test) == 8` (패딩 포함)  
- 하지만 `#pragma pack(1)`을 사용하면 **패딩이 제거되어 `sizeof(struct Test) == 6`**이 됨 → **파일 크기 절약 가능**.

---

### **❌ 단점**
| 단점 | 설명 |
|------|------|
| **CPU 성능 저하 가능성** | 일부 CPU(특히 x86/x86-64)는 메모리를 4 또는 8바이트 정렬로 읽는 것이 더 빠름. 1바이트 정렬 시 속도 저하 가능. |
| **연산 비용 증가** | 비정렬 데이터는 CPU가 자동으로 정렬하여 처리해야 하므로 추가적인 연산 비용 발생. |
| **이식성 저하** | 플랫폼별로 기본 구조체 정렬이 다를 수 있어, 다른 환경에서 문제가 생길 가능성 있음. |

**📌 성능 저하 예제**
```c
#pragma pack(1)
struct Test {
    char a;
    int b;
};
```
- **패딩 없이 5바이트**가 됨.
- CPU는 `b`(int)를 **4바이트 경계에서 읽는 것을 선호**하지만, 이 경우 **비정렬(unaligned) 메모리 접근**이 발생하여 속도가 느려질 수 있음.

---

## **🔍 `#pragma pack(n)`에서 `n`의 의미**
| `#pragma pack(n)` | 의미 |
|------------------|------|
| `#pragma pack(1)` | **1바이트 정렬 (패딩 제거)** |
| `#pragma pack(2)` | **2바이트 정렬 (2바이트 배수로 정렬)** |
| `#pragma pack(4)` | **4바이트 정렬 (기본 정렬보다 작은 경우 패딩 감소)** |
| `#pragma pack(8)` | **8바이트 정렬 (기본 정렬 유지, 성능 향상 가능)** |

📌 **예제: `#pragma pack(2)` 적용**
```c
#pragma pack(2)
struct Test {
    char a;   // 1바이트
    int b;    // 4바이트
    char c;   // 1바이트
};
#pragma pack()
```
- `a`는 1바이트
- `b`는 4바이트이지만, **2바이트 정렬로 인해 `a` 다음에 1바이트 패딩 추가**
- **총 크기: 2(패딩) + 4 + 1 = 7바이트** (원래는 8바이트일 수도 있음)

---

## **🚀 결론: `#pragma pack(1)`을 사용할 때 고려할 점**
### **📌 언제 사용해야 하나?**
✅ **메모리를 절약해야 하는 경우**  
✅ **파일 포맷, 네트워크 전송 시 패딩을 제거해야 하는 경우**  
✅ **하드웨어 레지스터 매핑이 필요한 경우**  

### **📌 언제 사용을 피해야 하나?**
❌ **CPU 성능이 중요한 경우 (특히 반복적인 구조체 연산이 많을 때)**  
❌ **이식성이 중요한 경우 (다른 플랫폼에서 정렬 방식이 달라질 수 있음)**  
❌ **구조체 내에서 `int`, `double`과 같은 정렬이 중요한 데이터가 많은 경우**  

### **📌 최적의 대안**
- `#pragma pack(1)`이 필요하지만 **속도 저하가 우려된다면**, **`#pragma pack(2)` 또는 `#pragma pack(4)`**를 사용하여 일부 정렬을 유지하면서 패딩을 줄일 수도 있음.
- 리눅스에서는 `__attribute__((packed))`를 **대체 옵션으로 사용 가능**:
  ```c
  struct __attribute__((packed)) Test { ... };
  ```

✔ **결론:** `#pragma pack(1)`은 메모리 절약에 유용하지만, CPU 성능 저하 가능성을 고려하여 신중하게 사용해야 함! 🚀
