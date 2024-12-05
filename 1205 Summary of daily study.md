# 2798. 블랙잭 [V2]

### 🌐 문제 링크:

https://www.acmicpc.net/problem/2798

# 💻 문제 설명

- 
    
    ## 문제
    
    카지노에서 제일 인기 있는 게임 블랙잭의 규칙은 상당히 쉽다. 카드의 합이 21을 넘지 않는 한도 내에서, 카드의 합을 최대한 크게 만드는 게임이다. 블랙잭은 카지노마다 다양한 규정이 있다.
    
    한국 최고의 블랙잭 고수 김정인은 새로운 블랙잭 규칙을 만들어 상근, 창영이와 게임하려고 한다.
    
    김정인 버전의 블랙잭에서 각 카드에는 양의 정수가 쓰여 있다. 그 다음, 딜러는 N장의 카드를 모두 숫자가 보이도록 바닥에 놓는다. 그런 후에 딜러는 숫자 M을 크게 외친다.
    
    이제 플레이어는 제한된 시간 안에 N장의 카드 중에서 3장의 카드를 골라야 한다. 블랙잭 변형 게임이기 때문에, 플레이어가 고른 카드의 합은 M을 넘지 않으면서 M과 최대한 가깝게 만들어야 한다.
    
    N장의 카드에 써져 있는 숫자가 주어졌을 때, M을 넘지 않으면서 M에 최대한 가까운 카드 3장의 합을 구해 출력하시오.
    

# **💡 풀이 과정**

- 
    
    ## 문제 접근
    
    문제는 주어진 N 장의 카드 중에서 3장의 카드를 선택하여 그 합이 M을 넘지 않으면서 최대한 M에 가까운 값을 구하는 문제이다.
    
    완전 탐색을 통해 모든 경우의 수를 구하고 가장 큰 3장의 카드의 합을 구하면 된다.
    
    3중 for 문을 통해 각 카드에 접근하여 3개의 카드의 합을 구하고, 각 3장의 카드의 합이 M을 넘지 않는 경우에 대해서만 결과를 갱신한다.
    
    현재까지의 최적의 결과를 저장하고, 새로운 합이 더 크지만 M을 넘지 않으면 결과를 갱신합니다.
    
    모든 경우를 탐색한 후, 최적의 결과를 출력한다.
    

# ✏️ **풀이 코드**

```python
import java.util.Scanner;

public class Main{
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);

        int N = sc.nextInt();
        int M = sc.nextInt();

        int[] arr = new int[N];
        for (int i = 0; i < N; i++) {
            arr[i] = sc.nextInt();
        }

        int max_anx = 0;

        for (int i = 0; i < N - 2; i++) {
            for (int j = i + 1; j < N - 1; j++) {
                for (int z = j + 1; z < N ; z++) {
                    int ans = arr[i] + arr[j] + arr[z];
                    if (ans <= M && ans > max_anx) {
                        max_anx = ans;
                    }
                }
            }
        }
        System.out.println(max_anx);
    }
}

```

# 📒 **풀이 후기**

패키지명을 제거하고, 클래스 이름을 Main으로 명명해야 런타임 에러가 발생하지 않는다.

문제에서 N,M 을 동시에 입력받도록 하였는데, java에서는 nextInt() 메서드를 통해 입력 스트림에서 **공백(space), 탭(tab), 줄바꿈(newline)** 등의 구분자를 기준으로 숫자를 읽는다. 그래서 공백으로 구분된 여러 값을 입력하면, nextInt() 를 여러 번 호출하여 각 값이 순서대로 변수에 저장할 수 있다.

# 2231. 분해합 [V2]
### 🌐 문제 링크:

https://www.acmicpc.net/problem/2231

# 💻 문제 설명

- 
    
    ## 문제
    
    어떤 자연수 N이 있을 때, 그 자연수 N의 분해합은 N과 N을 이루는 각 자리수의 합을 의미한다. 어떤 자연수 M의 분해합이 N인 경우, M을 N의 생성자라 한다. 예를 들어, 245의 분해합은 256(=245+2+4+5)이 된다. 따라서 245는 256의 생성자가 된다. 물론, 어떤 자연수의 경우에는 생성자가 없을 수도 있다. 반대로, 생성자가 여러 개인 자연수도 있을 수 있다.
    
    자연수 N이 주어졌을 때, N의 가장 작은 생성자를 구해내는 프로그램을 작성하시오.
    

# **💡 풀이 과정**

- 
    
    ## 문제 접근
    
    ### 나의 접근 방법
    
    주어진 수의 절반 크기부터 입력값까지 순회하며 각 자리수를 더한 값과 자기 자신을 합하여 입력값과 일치하는 가장 작은 생성자를 찾고, 일치하는 생성자가 없을 경우 0을 출력한다.
    

# ✏️ **풀이 코드**

```python
import java.util.Scanner;

public class Main {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);

        int N = sc.nextInt();
        int result = 0;

        for (int M = N/2; M <= N; M++) {
            int sumNum = M + digitSum(M); // 

            if (sumNum == N) { 
                result = M;
                System.out.println(result);
                break;
            }
            // 결과 출력 (0은 생성자가 없는 경우)
            if (M == N) {
                System.out.println(0);
            }
        }
    }

    // 각 자릿수의 합을 구하는 메서드
    public static int digitSum(int num) {
        int sum = 0;
        while (num > 0) {
            sum += num % 10; // 각 자릿수를 더함
            num /= 10;       // 다음 자릿수로 이동
        }
        return sum;
    }
}
```

## 📋 주요 코드 설명

```python

    public static int digitSum(int num) {
        int sum = 0;
        while (num > 0) {
            sum += num % 10; // 각 자릿수를 더함
            num /= 10;       // 다음 자릿수로 이동
        }
        return sum;
    }
```

Java에서는 각 자릿수에 직접 접근하는 내장 메서드가 없으므로, 반복문과 나눗셈 연산을 이용해 각 자릿수를 추출하는 메서드를 만들어서 활용한다. 이 메서드는 입력값을 10으로 나누며 마지막 자릿수를 구하고, 이를 누적 합산하여 결과를 반환한다.

# 📒 **풀이 후기**

Python에서는 sum(map(int, str(M)))을 통해 각 자리수의 합을 구할 수 있었지만, 자바에서는 각 자리수에 직접 접근하는 방법이 없어 직접 메서드를 만들어서 활용해야 하는데, 방법이 생각 나지 않아 코드를 참조하게 됐다.

# 19532. 수학은 비대면강의입니다[B2]
### 🌐 문제 링크:

https://www.acmicpc.net/problem/19532

# 💻 문제 설명

- 
    
    정수 a, b, c, d, e, f 를 주어졌을 때, 
    
    ax + by = c
    
    dx + ey = f
    
    해당 연립방정식에서 x, y의 값을 계산하여 출력하는 문제이다.
    

# **💡 풀이 과정**

- 
    
    ## 문제 접근
    
    ### 다른 사람의 접근 방법
    
    이 문제는 x와 y가 정수해로 제하되었고, 탐색 범위도 -999, 999까지 총 1999, 시간복작도는 
    
    O(N2)O(N^2) O(N2)에 해당하며, N=1999 라 하더라도 현대 컴퓨터로는 충분히 처리 가능하기 때문에 브루트 포스 즉 완전 탐색으로 해결하면 된다.
    
    # ✏️ **풀이 코드**
    
    - 이중 for문을 통해 x, y에 들어갈 수 있는 모든 수를 넣어서 조건에 만족하는 값이 나온다면 결과를 출력하면 된다.
        
        ```python
        import java.util.Scanner;
        
        public class Main {
            public static void main(String[] args) {
                Scanner sc = new Scanner(System.in);
        
                int a = sc.nextInt();
                int b = sc.nextInt();
                int c = sc.nextInt();
                int d = sc.nextInt();
                int e = sc.nextInt();
                int f = sc.nextInt();
        
                for (int x = -999; x <= 999; x++) {
                    for (int y = -999; y <= 999; y++) {
                        if ((a * x) + (b * y) == c && (d * x) + (e * y) == f) {
                            System.out.println(x + " " + y);
                            return;
                        }
                    }
                }
            }
        }
        ```
        
    
    ### 다른 사람의 접근 방법2
    
    너무 완전 탐색으로 접근 하는 거 같아서 다른 방법을 찾아보니, 아래처럼 X자 모양으로 수를 곱한 뒤 나눠지면 쉽게 x, y값을 구할 수 있다고 합니다.
    
    ![image](https://github.com/user-attachments/assets/e8118145-488a-473f-a834-ca46fe8139d8)
    
    출처: https://ssafy-story.tistory.com/68
    
    # ✏️ **풀이 코드**
    
    ```python
    import java.util.Scanner;
    
    public class Main {
        public static void main(String[] args) {
            Scanner sc = new Scanner(System.in);
    
            int a = sc.nextInt();
            int b = sc.nextInt();
            int c = sc.nextInt();
            int d = sc.nextInt();
            int e = sc.nextInt();
            int f = sc.nextInt();
    
            int x = (c * e - b * f) / (a * e - b * d);
            int y = (c * d - a * f) / (b * d - a * e);
            System.out.println(x + " " + y);
        }
    }
    ```
    

# 📒 **풀이 후기**

System.out.println()은 하나의 인자값만 받기 때문에, “System.out.println(x, y);” 이런식으로 사용하면 컴파일 에러가 발생한다. 그렇기 때문에 여러 값을 출력할려면 하나의 문자열로 만들어서 출력해야 한다.

또한, **break**는 현재 실행 중인 반복문만 종료시키고, 그 이후의 반복문은 계속 실행된다. 만약 모든 반복문을 종료시키고 싶다면, Java에서는 return을 사용하여 메서드를 종료시킬 수 있다.

# 1436. 영화감독 슘 [S5]
### 🌐 문제 링크:

https://www.acmicpc.net/problem/1436

# 💻 문제 설명

- 문제 설명
    
    종말의 수란 어떤 수에 6이 적어도 3개 이상 연속으로 들어가는 수를 말한다.
    
    제일 작은 종말의 수는 666이고, 그 다음으로 큰 수는 1666, 2666, 3666, .... 이다.
    
    숌은 첫 번째 영화의 제목은 "세상의 종말 666", 두 번째 영화의 제목은 "세상의 종말 1666"와 같이 이름을 지을 것이다. 일반화해서 생각하면, N번째 영화의 제목은 세상의 종말 (N번째로 작은 종말의 수) 와 같다.
    
    숌이 만든 N번째 영화의 제목에 들어간 수를 출력하는 프로그램을 작성하시오. 숌은 이 시리즈를 항상 차례대로 만들고, 다른 영화는 만들지 않는다.
    

# **💡 풀이 과정**

## 문제 접근

N번째 영화 제목은 **"666"**이라는 숫자가 연속으로 포함된 수들 중 N번째로 **작은 수**를 의미하므로, 반복문을 통해 변수의 값을 666부터 시작해 숫자를 증가시키며 "666"이 포함된 수를 확인하고, 이를 카운트하여 N번째에 도달하면 해당 값을 출력하는 방식으로 해결하였다.

처음에 배열에 “666”이 포함된 모든 값을 직접 입력하여 저장하고 인덱싱을 통해 접근하는 방법이 있지만, 모든 값을 직접 넣는 방법은 불편하다. 그렇기 때문에 영화 제목에 패턴을 파악하고, 반복물을 통해 변수의 값을 666부터 값을 올리면서 해당 값이 “666”이 포함되어 있는지 확인하고 카운트 변수를 통해 갱신하여 이 값이 n값이 되면 반복문을 종료하고 해당 값을 출력하였다.

### 영화 제목 패턴

제목은 6이 적어도 3개 이상 연속으로 들어가고 크기 순서대로 제목을 짓기 때문에 566 다음에 6660이 오게된다.

```python
666
1666
2666
3666
4666
5666
6660
6661
...
```

# ✏️ **풀이 코드**

```python
import java.util.ArrayList;
import java.util.Scanner;

public class Main {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        int N = sc.nextInt();

        int st = 666;
        int count = 0;
        ArrayList<Integer> lst = new ArrayList<>();

        while (true) {
            if (String.valueOf(st).contains("666")) { // 문자열로 변환 후 "666" 포함 여부 확인
                lst.add(st);
                count++;
                if (count == N) {
                    break;
                }
            }
            st++;
        }

        System.out.println(lst.get(N - 1)); 
    }
}
```

# 📋 주요 코드 설명

```python
if (String.valueOf(st).contains("666")) { // 문자열로 변환 후 "666" 포함 여부 확인
    lst.add(st);
    count++;
    if (count == N) {
        break;
		    }
		}
		st++;
}
```

숫자를 문자열로 변환하기 위해 **String.valueOf()** 메서드를 통해 확인한다.
이렇게 문자열로 만든 후, String.contains()메서드를 사용해 "666"의 포함 여부를 확인한다.

- String.contains()는 문자열에만 사용 가능한 메서드이며, 특정 문자열이 포함되어 있는지 확인할 때 사용한다.

# 📒 **풀이 후기**

처음에 영화 제목 패턴에 대해 이해하지 못했고, 이해했어도 어떻게 접근해야 할지 생각하지 못했다. 그런데 이 문제가 브루트 포스 문제라는 것을 생각하면서 반복문을 통해 값을 증가시키면서 조건울 두어서 조건에 해당하는 값을 배열에 저장하고 종료하여 배열의 인덱스를 통해 출력하는 방법으로 도출하였다.

오랜만에 스스로 문제를 풀어서 좋기는 하지만, in 키워드 사용법과 반복문의 시작값과 조건등을 잘못 설정하여 오류가 발생해서, print()를 통해 디버깅하여 알았기 때문에 시간이 좀 걸렸다.

문제 접근 방법을 기술할 때 어떤 변수나 반복문 등 에 대한 설명을 꼼꼼히 적고, 디버깅 필요 없이 코딩 시간을 많이 줄이는 방향으로 나아가야겠다.

# 2839. 설탕 배달(실버4)
### 🌐 문제 링크:

https://www.acmicpc.net/problem/2839

# 💻 문제 설명

- 
    
    상근이는 설탕 공장에서 설탕을 사탕가게에 N킬로그램을 배달해야한다.
    설탕 공장에서 만드는 설탕은 **3, 5** 킬로그램 봉지 두 종류가 있다. 이때 상근이는 최대한 적은 봉지를 들고 가려고 한다.
    상근이가 입력받은 N킬로그램 배달해야 할 때, 봉지 몇개를 가져가면 되는지 그 수를 구하는 프로그램을 작성해라.
    

# **💡 풀이 과정**

## 문제 접근

최대한 적은 봉지를 가져가기 위해서는 가장 큰 숫자인 5킬로 그램으로 최대한 가져가고 나머지는 3킬로 그램 봉지로 가져가면 된다.
만약 N이 5를 나눠어지고 나머지가 3의 배수라면, 나누었을 때 몫과 나머지를 각각 변수에 저장하고 몫은 봉지의 개수에 누적시키고 나머지는 3의 배수인지 확인하여 -1을 출력 유무를 확인한다.

코드의 조건 흐름은 다음과 같다.

1. N이 3의 배수인지 확인, 3의 배수가 아니라면 5보다 큰지 확인

1-1.  5로 나눠었을 때 나머지가 3의 배수인지 확인, 맞다면 5로 나눠었을 때 목을 결과값에 누적시키고 나머지는 3과 나눠어 몫을 결과값에 누적시킨다.
1-2.  둘다 나눠어 지지 않는다면 -1을 출력 된다면, 5로 나눠었을 때 몫은 결과값에 저장하고 나머지 값은 3의 배수인지 확인

문제가 9같은 경우 5로 나눠어 지고 나머지가 3의 배수로 안나눠 진다. 그런데 3의 배수이기 때문에 3을 먼저 나눠어야 한다.

# ✏️ **풀이 코드**

```python
import java.util.Scanner;

public class Main {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);

        int N = sc.nextInt();
        int ans = -1; // 기본값을 -1로 설정 (나눌 수 없는 경우)

        if (N % 3 == 0) {
            ans = N / 3;
        }
        if (N >= 5) {
            int bong = N % 5;
            if (bong % 3 == 0) {
                int alternative = (N / 5) + (bong / 3);
                if (ans == -1) { // 아직 초기화되지 않았다면
                    ans = alternative;
                } else { // 더 작은 값을 선택
                    ans = Math.min(ans, alternative);
                }
            }
        }
        System.out.println(ans);
    }
}
```

문제에서 핵심은 가장 적은 봉지로 배달을 가는 것이다. 그러기 위해서는 가장 큰 단위인 5로 최대한 나누고 나머지를 3으로 나누는 방식이 최적의 해를 보장한다. 하지만 3으로만 나눠어 지는 경우아 있어 3으로 먼저 나눠어 지는지 확인한 것 같다. 그리고 이 코드는 5로 최대한 나눈 뒤 남은 값이 3의 배수가 아니면 -1을 출력하는 구조로, 5의 사용을 줄이고 3의 배수로 나누는 다른 조합을 탐색하지 않아 11과 같은 예제에서 최적의 봉지 개수를 찾지 못하게 된다.

해결이 되지 않아 다른사람의 코드를 참조했습니다.

이 문제가 요구하는 것이 최소한의 봉지만 사용하여 설탕을 담는 것이기 때문에 나올 수 있는 경우의 수는 총 4가지 입니다.

1. 가장 큰 단위인 5만으로 나눠 담는 것
2. 최소한의 3과 최대한의 5를 사용하여 나눠 담는 것
3. 3으로 나눠 담는 것
4. 나눠지지 않을 때

출처: https://ooyoung.tistory.com/81

경우의 수를 적용한 코드는 다음과 같다.

# ✏️ **풀이 코드**

```python
import java.util.Scanner;

public class Main {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);

        int sugar = sc.nextInt(); // 설탕 무게 입력
        int bag = 0; // 봉지 수 초기화

        // 설탕 무게가 0 이상일 때 반복
        while (sugar >= 0) {
            if (sugar % 5 == 0) { 
                bag += (sugar / 5); 
                System.out.println(bag); 
                break; 
            }
            sugar -= 3; 
            bag += 1; 
        }
        
        // 3과 5로 나눠지지 않으면 -1 출력
        if (sugar < 0) {
            System.out.println(-1);
        }
    }
}
```

 먼저 입력값이 5의 배수인지 확인하고, 5로 나눠떨어지지 않을 경우 설탕의 무게에서 3씩 줄이면서 봉지의 개수를 1씩 증가시킨다. 이렇게 하면 설탕의 무게를 5와 3의 조합으로 나눌 수 있는 경우를 모두 탐색할 수 있으며, 설탕의 무게가 음수가 되기 전에 5나 3으로 나누어지지 않으면 -1을 출력한다.

# 📒 **풀이 후기**

조건들을 나열했지만 어떤 것이 우선순위이고 예제 문제에 대한 조건들을 다 확인하지 않고 짜서 틀리게 된것 같다. 참조했던 코드의 설명처럼 예제 문제에 대한 경우의 수를 나열하고, 해당 조건들을 모두 만족할 수 있는 코드를 구현하는 방향으로 문제를 풀어나가는 것이 좋은 방법인거 같다.
