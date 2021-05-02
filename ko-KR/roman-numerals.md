# 로마 숫자

**[이 챕터의 모든 코드는 여기에서 확인할 수 있다.](https://github.com/MiryangJung/learn-go-with-tests-ko/tree/master/roman-numerals)**

일부 기업은 면접 과정의 일환으로 [로마 숫자 카타](http://codingdojo.org/kata/RomanNumerals/)를 요청할 것이다. 이 장에서는 TDD를 사용하여 그 문제를 어떻게 해결할 수 있는지 설명할 것이다. 

우리는 [아라비아 숫자](https://en.wikipedia.org/wiki/Arabic_numerals)(0부터 9까지)를 로마 숫자로 변환하는 함수를 작성할 것이다. 

만약 여러분이 [로마 숫자](https://en.wikipedia.org/wiki/Roman_numerals)를 들어본적 없다면, 이것은 로마인들이 숫자를 적은 방식이다. 

기호들을 함께 붙이는 것으로 만들 수 있으며, 해당 기호들은 숫자를 뜻한다. 

그러므로 `I` 는 "하나"이고, `III` 는 "셋"이다.

쉬워 보이지만, 여기에는 몇 가지 흥미로운 규칙이 있다. `V` 는 다섯을 뜻하지만, `IV` 는 4이다 (`IIII` 가 아니다).

`MCMLXXXIV` 는 1984이다. 이것은 복잡해 보이고, 어떻게 코드를 작성하여 이것을 처음부터 알맞게 알아낼 수 있는지 상상하기 어렵다. 

이 책이 강조하는 것 처럼, 소프트웨어 개발자에게 중요한 한 가지 스킬은 _유용한_ 기능의 "얇은 수직의 슬라이스"를 시도하고 밝혀내는 것이며, 그리고 **반복**하는 것이다. TDD 워크플로우는 반복적인 개발을 용이하게 한다.

그러면 1984 보다는, 1로 시작해 보자. 

## 테스트부터 작성하기

```go
func TestRomanNumerals(t *testing.T) {
	got := ConvertToRoman(1)
	want := "I"

	if got != want {
		t.Errorf("got %q, want %q", got, want)
	}
}
```

만약 이 책의 이 정도까지 도달했다면 이것은 아마 지루하고 반복적으로 느껴질 것이다. 그건 좋은 일이다. 

## 테스트 실행해보기

`./numeral_test.go:6:9: undefined: ConvertToRoman`

컴파일러가 길을 안내하도록 하자. 

## 테스트를 실행할 최소한의 코드를 작성하고 실패한 테스트 출력을 확인하기

함수를 생성하되 아직 테스트를 통과하지는 못 한다. 언제나 테스트가 당신이 예상하던 대로 실패하는지 확인해야 한다.

```go
func ConvertToRoman(arabic int) string {
	return ""
}
```

이것은 지금 이렇게 실행될 것이다.

```go
=== RUN   TestRomanNumerals
--- FAIL: TestRomanNumerals (0.00s)
    numeral_test.go:10: got '', want 'I'
FAIL
```

## 통과할 만큼 충분한 코드를 작성하기

```go
func ConvertToRoman(arabic int) string {
	return "I"
}
```

## 리팩토링 하기

아직은 리팩토링 할 것이 많지 않다. 

결과를 하드코딩 하는 것이 얼마나 이상한지 _알고 있지만,_ 테스트 주도 개발을 통해서는 우리는 "빨간색"에서 가능한 만큼 오랫동안 벗어나 있는 것을 원한다. 우리가 많은 것을 달성하지 못한 것 처럼 _느껴지지만_ 우리는 API를 정의 했고 우리의 규칙 중 하나를 잡아내는 테스트를 받았다. 만약 그 "진짜" 코드가 꽤나 바보 같아도 말이다. 

이제 그 불편한 느낌을 이용해서 우리가 조금 덜 바보같은 코드를 작성하게 만드는 새로운 테스트를 작성해 보자.

## 테스트부터 작성하기

우리는 서브 테스트를 이용하여 테스트들을 그룹화 할 수 있다. 

```go
func TestRomanNumerals(t *testing.T) {
	t.Run("1 gets converted to I", func(t *testing.T) {
		got := ConvertToRoman(1)
		want := "I"

		if got != want {
			t.Errorf("got %q, want %q", got, want)
		}
	})

	t.Run("2 gets converted to II", func(t *testing.T) {
		got := ConvertToRoman(2)
		want := "II"

		if got != want {
			t.Errorf("got %q, want %q", got, want)
		}
	})
}
```

## 테스트 실행해보기

```
=== RUN   TestRomanNumerals/2_gets_converted_to_II
    --- FAIL: TestRomanNumerals/2_gets_converted_to_II (0.00s)
        numeral_test.go:20: got 'I', want 'II'
```

별로 놀랍지 않다. 

## 통과할 만큼 충분한 코드를 작성하기

```go
func ConvertToRoman(arabic int) string {
	if arabic == 2 {
		return "II"
	}
	return "I"
}
```

맞다, 아직도 여전히 우리가 정말 이 문제를 제대로 해결하고 있는 것 같지 않은 것 처럼 느껴진다. 그러므로 우리를 앞으로 이끌어 줄 수 있는 테스트들을 더 작성해야한다. 

## 리팩토링 하기

우리의 테스트에는 약간의 반복이 있다. "주어진 입력 X에 대해 우리는 Y를 기대한다"의 문제처럼 느껴지는 것을 테스트할 때 우리는 테이블 기반의 테스트를 이용해야한다. 

```go
func TestRomanNumerals(t *testing.T) {
	cases := []struct {
		Description string
		Arabic      int
		Want        string
	}{
		{"1 gets converted to I", 1, "I"},
		{"2 gets converted to II", 2, "II"},
	}

	for _, test := range cases {
		t.Run(test.Description, func(t *testing.T) {
			got := ConvertToRoman(test.Arabic)
			if got != test.Want {
				t.Errorf("got %q, want %q", got, test.Want)
			}
		})
	}
}
```

우리는 이제 다른 테스트 보일러 플레이트를 더 작성하는 일 없이 쉽게 더 많은 테스트 케이스를 추가할 수 있다. 

계속해서 3을 해 보자.

## 테스트부터 작성하기

아래 경우를 테스트 케이스에 추가하자. 

```go
{"3 gets converted to III", 3, "III"},
```

## 테스트 실행해보기

```
=== RUN   TestRomanNumerals/3_gets_converted_to_III
    --- FAIL: TestRomanNumerals/3_gets_converted_to_III (0.00s)
        numeral_test.go:20: got 'I', want 'III'
```

## 통과할 만큼 충분한 코드를 작성하기

```go
func ConvertToRoman(arabic int) string {
	if arabic == 3 {
		return "III"
	}
	if arabic == 2 {
		return "II"
	}
	return "I"
}
```

## 리팩토링 하기

좋다. 이런 가정 문장들을 즐기지 않기 시작되었다. 이 코드를 충분히 자세하게 살펴보면 우리는 `아라비아 숫자`의 크기를 바탕으로 `I` 문자열을 만들고 있다는 것을 알 수 있다. 

우리는 조금 더 복잡한 숫자들에 대해서는 약간의 산수 계산과 문자열 붙이기를 하게 될 것을 알고 있다. 

이러한 생각을 염두에 두고 리팩토링을 시도 해 보자. 이것은 최종 해결 방법에 _아마 알맞지 않은_ 방법일 수도 있지만 괜찮다. 우리는 언제든지 코드를 던져 버리고 우리를 안내할 테스트로 새로 시작할 수 있다. 

```go
func ConvertToRoman(arabic int) string {

	var result strings.Builder

	for i:=0; i<arabic; i++ {
		result.WriteString("I")
	}

	return result.String()
}
```

[`strings.Builder`](https://golang.org/pkg/strings/#Builder) 를 전에 사용해 본 적이 없을 수도 있다. 

> 빌더는 Write 메서드를 이용해 문자열을 효율적으로 만드는 데에 이용되며 또한 메모리 복사를 최소화 한다. 

일반적으로 실제 성능 문제가 발생하기 전 까지는 이러한 최적화에 신경쓰지 않겠지만, 코드의 양이 수동으로 문자열에 수동으로 추가하는 것 보다 그다지 크지 않기 때문에 우리는 더 빠른 접근을 사용하는 것이 좋다. 

그 코드는 더 좋아보이며, 도메인을 _우리가 현재 알고 있는 것 처럼_ 설명한다. 

### 로마인들 또한 DRY에 빠져 있었다...

이제 더욱 복잡해 지기 시작한다. 지혜로운 로마인들은 반복되는 문자는 읽기와 세기에 어려워 질 것이라고 생각했다. 그래서 로마 숫자의 규칙은 똑같은 문자를 세 번 이상 반복할 수 없다는 것이다. 

대신에 다음으로 가장 높은 기호를 가지고 다른 기호를 그 왼편에 놓아 "추출"한다. 모든 기호가 추출자로 쓰일 수는 없으며, 오직 I (1), X (10) 그리고 C (100) 만 가능하다.



예를 들어 `5` 의 로마 숫자는 `V` 이다. 4를 만들기 위해서는 `IIII` 가 아니라, `IV` 가 된다.

## 테스트부터 작성하기

```go
{"4 gets converted to IV (can't repeat more than 3 times)", 4, "IV"},
```

## 테스트 실행해보기

```
=== RUN   TestRomanNumerals/4_gets_converted_to_IV_(cant_repeat_more_than_3_times)
    --- FAIL: TestRomanNumerals/4_gets_converted_to_IV_(cant_repeat_more_than_3_times) (0.00s)
        numeral_test.go:24: got 'IIII', want 'IV'
```

## 통과할 만큼 충분한 코드를 작성하기

```go
func ConvertToRoman(arabic int) string {

	if arabic == 4 {
		return "IV"
	}

	var result strings.Builder

	for i:=0; i<arabic; i++ {
		result.WriteString("I")
	}

	return result.String()
}
```

## 리팩토링 하기

우리가 문자열 만드는 패턴을 무너 뜨린 것이 마음에 들지 않으므로 그것을 계속하고 싶다. 

```go
func ConvertToRoman(arabic int) string {

	var result strings.Builder

	for i := arabic; i > 0; i-- {
		if i == 4 {
			result.WriteString("IV")
			break
		}
		result.WriteString("I")
	}

	return result.String()
}
```

4가 현재 우리의 생각에 들어 맞기 위해서 이제 아라비아 숫자에서 숫자를 세고, 진행해 감에 따라 기호를 문자열에 붙인다. 이것이 장기적으로도 효과가 있을 지는 모르겠지만 어디 지켜보자. 

5를 해치워 보자. 

## 테스트부터 작성하기

```go
{"5 gets converted to V", 5, "V"},
```

## 테스트 실행해보기

```
=== RUN   TestRomanNumerals/5_gets_converted_to_V
    --- FAIL: TestRomanNumerals/5_gets_converted_to_V (0.00s)
        numeral_test.go:25: got 'IIV', want 'V'
```

## 통과할 만큼 충분한 코드를 작성하기

그냥 4를 위해 했던 접근 방식을 똑같이 해 보자. 

```go
func ConvertToRoman(arabic int) string {

	var result strings.Builder

	for i := arabic; i > 0; i-- {
		if i == 5 {
			result.WriteString("V")
			break
		}
		if i == 4 {
			result.WriteString("IV")
			break
		}
		result.WriteString("I")
	}

	return result.String()
}
```

## 리팩토링 하기

이와 같은 루프에서의 반복은 보통 호출을 기다리는 추상화의 표시이다. 짧게 반복되는 루프는 가독성을 위한ㄴ 효과적인 도구가 될 수 있지만, 다른 것을 알려줄 수도 있다. 

우리는 아라비아 숫자를 반복하고 있다. 만약 우리가 특정한 기호를 건드리게 되면 `break`을 호출하게 되지만, 우리가 실제로 하고 있는 것은 서투른 방법으로 `i` 를 추출하는 것이다.  

```go
func ConvertToRoman(arabic int) string {

	var result strings.Builder

	for arabic > 0 {
		switch {
		case arabic > 4:
			result.WriteString("V")
			arabic -= 5
		case arabic > 3:
			result.WriteString("IV")
			arabic -= 4
		default:
			result.WriteString("I")
			arabic--
		}
	}

	return result.String()
}

```

- 코드에서 읽히는 신호로 보아, 몇 가지 매우 기본적인 시나리오의 우리 테스트에서 볼 때 로마 숫자를 만들기 위해서는 기호를 추가함에 따라 `아라비아 숫자` 에서 추출해야 한다. 
- `for` 반복문은 더이상 `i`에 의존하지 않고 대신 우리가 `아라비아 숫자에서` 충분한 기호를 추출해 낼 때까지 계속해서 문자열을 만들 것이다. 

이 접근 방식이 6 (VI), 7 (VII) 그리고 8 (VIII) 에도 유효할 것임을 확신한다. 그럼에도 불구하고 테스트에 테스트 케이스를 추가하고 확인 하자 (간결함을 위해 코드를 추가하지는 않겠다만, 확신이 서지 않는다면 github에서 예제를 확인할 수 있다).

9는 다음 숫자의 표현에서 `I`를 추출해야 한다는 점에서 4의 것과 같은 규칙을 따른다. 10은 로마 숫자에서 `X`로 표현 되어 있다. 따라서 9는 `IX` 이어야 한다. 

## 테스트부터 작성하기

```go
{"9 gets converted to IX", 9, "IX"}
```

## 테스트 실행해보기

```
=== RUN   TestRomanNumerals/9_gets_converted_to_IX
    --- FAIL: TestRomanNumerals/9_gets_converted_to_IX (0.00s)
        numeral_test.go:29: got 'VIV', want 'IX'
```

## 통과할 만큼 충분한 코드를 작성하기

이전과 같은 접근 방식을 사용할 수 있어야 한다. 

```go
case arabic > 8:
    result.WriteString("IX")
    arabic -= 9
```

## 리팩토링 하기

코드가 여전히 어딘가 리팩토링 해야한다고 말 하는 것 처럼 느껴지지만 나에겐 완전히 명백하지는 않으므로, 계속 해 나가보자. 

이것 역시 코드를 생략할 것이지만, 테스트에 `10`은 `X`가 되어야 한다는 테스트 케이스를 추가하고 더 읽기 전에 그것이 테스트를 통과하도록 하자. 

최대 39까지 우리의 코드가 작동할 것으로 확신하기에, 나는 이와 같은 몇 테스트를 더 추가했다. 

```go
{"10 gets converted to X", 10, "X"},
{"14 gets converted to XIV", 14, "XIV"},
{"18 gets converted to XVIII", 18, "XVIII"},
{"20 gets converted to XX", 20, "XX"},
{"39 gets converted to XXXIX", 39, "XXXIX"},
```

만약 객체 지향 프로그래밍을 해 봤다면, `switch` 문장은 조금 의심스럽게 봐야한다는 것을 알 것이다. 일반적으로 어떤 필수적인 코드 내에서 개념이나 데이터를 잡아내는데, 사실은 클래스의 구조에서 대신 찾아낼 수도 있다 (일부에서 말하고 싶어하는 것 처럼 말이다). 

Go는 엄격하게 객체 지향은 아니지만 그것이 객체 지향이 제안한 교훈을 무시한다는 것은 아니다. 

우리의 `switch` 문은 행동과 함께 로마 숫자의 어떤 진실을 묘사하고 있다. 

우리는 행동으로부터 데이터를 분리함으로써 이것을 리팩토링 할 수 있다. 

```go
type RomanNumeral struct {
	Value  int
	Symbol string
}

var allRomanNumerals = []RomanNumeral {
	{10, "X"},
	{9, "IX"},
	{5, "V"},
	{4, "IV"},
	{1, "I"},
}

func ConvertToRoman(arabic int) string {

	var result strings.Builder

	for _, numeral := range allRomanNumerals {
		for arabic >= numeral.Value {
			result.WriteString(numeral.Symbol)
			arabic -= numeral.Value
		}
	}

	return result.String()
}
```

이것은 훨씬 낫게 느껴진다. 우리는 숫자에 관한 몇 가지 규칙을 알고리즘에 숨겨진 것으로 하는 것이 아니라 데이터로 선언했다. 그리고 우리는 아라비아 숫자를 통해 어떻게 동작하는지 볼 수 있으며, 만약 결과가 맞는다면 기호를 추가하려고 한다. 

이 추상화가 더 큰 숫자들에도 효과가 있을까? 로마 숫자 50인 `L`에도 작동 하도록 테스트를 연장해 보자.

아래는 몇 가지 테스트 케이스들이다. 통과할 수 있도록 시도해 보자. 

```go
{"40 gets converted to XL", 40, "XL"},
{"47 gets converted to XLVII", 47, "XLVII"},
{"49 gets converted to XLIX", 49, "XLIX"},
{"50 gets converted to L", 50, "L"},
```

도움이 필요 한가? [이 gist](https://gist.github.com/pamelafox/6c7b948213ba55332d86efd0f0b037de) 에서 어떤 기호를 추가하는지 확인할 수 있다. 


## 그리고 나머지!

이것이 남아있는 기호들이다. 

| Arabic        | Roman           |
| ------------- |:-------------:|
| 100     | C      |
| 500 | D      |
| 1000 | M      |

남아있는 기호들에도 똑같은 접근 방식을 이용하자. 그냥 테스트와 기호 배열 모두에 데이터를 추가하는 것의 문제이다. 

코드가 `1984`: `MCMLXXXIV` 에도 작동하는가?

아래는 최종 테스트이다. 

```go
func TestRomanNumerals(t *testing.T) {
	cases := []struct {
		Arabic int
		Roman  string
	}{
		{Arabic: 1, Roman: "I"},
		{Arabic: 2, Roman: "II"},
		{Arabic: 3, Roman: "III"},
		{Arabic: 4, Roman: "IV"},
		{Arabic: 5, Roman: "V"},
		{Arabic: 6, Roman: "VI"},
		{Arabic: 7, Roman: "VII"},
		{Arabic: 8, Roman: "VIII"},
		{Arabic: 9, Roman: "IX"},
		{Arabic: 10, Roman: "X"},
		{Arabic: 14, Roman: "XIV"},
		{Arabic: 18, Roman: "XVIII"},
		{Arabic: 20, Roman: "XX"},
		{Arabic: 39, Roman: "XXXIX"},
		{Arabic: 40, Roman: "XL"},
		{Arabic: 47, Roman: "XLVII"},
		{Arabic: 49, Roman: "XLIX"},
		{Arabic: 50, Roman: "L"},
		{Arabic: 100, Roman: "C"},
		{Arabic: 90, Roman: "XC"},
		{Arabic: 400, Roman: "CD"},
		{Arabic: 500, Roman: "D"},
		{Arabic: 900, Roman: "CM"},
		{Arabic: 1000, Roman: "M"},
		{Arabic: 1984, Roman: "MCMLXXXIV"},
		{Arabic: 3999, Roman: "MMMCMXCIX"},
		{Arabic: 2014, Roman: "MMXIV"},
		{Arabic: 1006, Roman: "MVI"},
		{Arabic: 798, Roman: "DCCXCVIII"},
	}
	for _, test := range cases {
		t.Run(fmt.Sprintf("%d gets converted to %q", test.Arabic, test.Roman), func(t *testing.T) {
			got := ConvertToRoman(test.Arabic)
			if got != test.Roman {
				t.Errorf("got %q, want %q", got, test.Roman)
			}
		})
	}
}
```

- 정보에서 _데이터_ 가 충분히 설명되었다고 느껴 설명을 제거했다. 
- 조금 더 자신감을 주기 위해 내가 찾아낸 몇 가지 엣지 케이스를 더 추가했다. 테이블 기반 테스트에서는 이 작업이 매우 쉽다. 

알고리즘을 바꾸지 않고, `allRomanNumerals` 배열만 업데이트 하면 되었다. 

```go
var allRomanNumerals = []RomanNumeral{
	{1000, "M"},
	{900, "CM"},
	{500, "D"},
	{400, "CD"},
	{100, "C"},
	{90, "XC"},
	{50, "L"},
	{40, "XL"},
	{10, "X"},
	{9, "IX"},
	{5, "V"},
	{4, "IV"},
	{1, "I"},
}
```

## 로마 숫자 파싱하기

아직 끝나지 않았다. 다음으로 우리는 로마 숫자로 부터 `int`를 변환하는 함수를 작성할 것이다. 


## 테스트부터 작성하기

약간의 리팩토링만 거치면 테스트 케이스들을 재사용 할 수 있다. 

`var` 블록에 있는 패키지 변수 처럼 `cases` 변수를 테스트 바깥으로 이동시키자. 

```go
func TestConvertingToArabic(t *testing.T) {
	for _, test := range cases[:1] {
		t.Run(fmt.Sprintf("%q gets converted to %d", test.Roman, test.Arabic), func(t *testing.T) {
			got := ConvertToArabic(test.Roman)
			if got != test.Arabic {
				t.Errorf("got %d, want %d", got, test.Arabic)
			}
		})
	}
}
```

참고로 모든 테스트를 한 번에 통과 시키는 것은 너무 큰 도약이므로 슬라이스 기능을 이용하여 테스트들 중 하나의 테스트 (`cases[:1]`) 만을 실행 한다. 

## 테스트 실행해보기

```
./numeral_test.go:60:11: undefined: ConvertToArabic
```

## 테스트를 실행할 최소한의 코드를 작성하고 실패한 테스트 출력을 확인하기

새로운 함수의 정의를 추가한다. 

```go
func ConvertToArabic(roman string) int {
	return 0
}
```

이제 테스트가 실행되고 실패 할 것이다. 

```
--- FAIL: TestConvertingToArabic (0.00s)
    --- FAIL: TestConvertingToArabic/'I'_gets_converted_to_1 (0.00s)
        numeral_test.go:62: got 0, want 1
```

## 통과할 만큼 충분한 코드를 작성하기

우리는 무엇을 해야할 지 알고 있다. 

```go
func ConvertToArabic(roman string) int {
	return 1
}
```

다음으로, 테스트에서 슬라이스의 인덱스를 변경하여 다음 테스트 케이스로 이동한다 (예: `cases[:2]`). 생각할 수 있는 가장 멍청한 코드로 그것을 통과시키고, 세 번째 테스트 케이스에 대해서도 바보같은 코드를 계속해서 작성하자. 이것이 내 바보같은 코드이다. 

```go
func ConvertToArabic(roman string) int {
	if roman == "III" {
		return 3
	}
	if roman == "II" {
		return 2
	}
	return 1
}
```

_작동하는 진짜 코드_ 의 멍청함을 통해 우리는 저번 처럼 패턴을 확인하기 시작할 수 있다. 받은 입력을 반복해야하며 _무언가_ 를 만들어야 하는데, 이 경우에는 총 합계이다. 

```go
func ConvertToArabic(roman string) int {
	total := 0
	for range roman {
		total++
	}
	return total
}
```
