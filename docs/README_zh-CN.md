<h1 align="center">EmbeddedButton</h1>

<p align="center">
<a href="https://github.com/530china/EmbeddedButton/blob/master/LICENSE" target="blank">
<img src="https://img.shields.io/github/license/rahuldkjain/github-profile-readme-generator?style=flat-square" alt="github-profile-readme-generator license" />
</a>
<a href="https://github.com/530china/EmbeddedButton/stargazers" target="blank">
<img src="https://img.shields.io/github/stars/rahuldkjain/github-profile-readme-generator?style=flat-square" alt="github-profile-readme-generator stars"/>
</a>
<a href="https://github.com/530china/EmbeddedButton/issues" target="blank">
<img src="https://img.shields.io/github/issues/rahuldkjain/github-profile-readme-generator?style=flat-square" alt="github-profile-readme-generator issues"/>
</a>
<a href="https://github.com/530china/EmbeddedButton/pulls" target="blank">
<img src="https://img.shields.io/github/issues-pr/rahuldkjain/github-profile-readme-generator?style=flat-square" alt="github-profile-readme-generator pull-requests"/>
</a>
</p>

<h2>? ���</h2>
EmbeddedButton��һ�������������õ�Ƕ��ʽ��������ģ�飬��������չ����;

- ֧�ֶ��������������̰������ȶ��ְ����¼���
- ģ��ͨ��������ԭ����������������߼���֧��;
- ���Ĵ����ȡ����������ʽ��֧��λ�����ֵƥ�䣬�����û�����ֵ���壬������ֵ�������û�ͨ��**���ü�ֵƥ�����**���ж��壬����������ʽ�޸Ĵ��룬����Լ�ǿ��

## ? ����

> 1.�����򵥼���ԭ��֧�������������ж��߼�
- ֻҪ��ֵ���㣬ʱ��tick++
- ֻҪ����״̬�����仯���ı�һ�μ�ֵ��**__append_bit()**����tickʱ�����㣨ȷ��tickΪ���»�̧���ʱ�䣩
- ��tickʱ��ĳ��̼�����̧����Ϊһ��״̬�������ж����ݣ����Ժܺõ�ʵ�ֶ̰������Ȳ�����

> 2.ʹ��C����ʵ�֣���������λ������ʵ��ÿ��������ֵ�Ķ����Ƽ�¼��ʾ��1�����£�0�����ɿ�

��ֵ | ˵��
--- | ---
0b0 | δ����
0b010 | ����
0b01010 | ˫��
0b01010...n | n����
0b011 | ������ʼ
0b0111| ��������
0b01110|��������
0b01011|�̰�Ȼ�󳤰�
0b0101011 | ˫��Ȼ�󳤰�
0b01010..n11 | n����Ȼ�󳤰�

> 3.���Ĵ����ȡ����������ʽ��֧��λ�����ֵƥ�䣺
- �ؼ����ݽṹ����ֵƥ��������ñ�
```c
typedef struct {
    key_value_type_t operand;           // ������
    kv_match_operator_type_t operator;  // ������
    key_value_type_t tar_result;        // Ŀ����
    void (*kv_func_cb)(void*);          // ����ƥ�����õĻص�����
} key_value_match_map_t;

```
- �ؼ��㷨��
```c
key_value_type_t operand_origin = button->kv_match_map_ptr[i].operand;
key_value_type_t operand_result = button->kv_match_map_ptr[i].operand;
kv_match_operator_type_t operator =button->kv_match_map_ptr[i].operator;
key_value_type_t tar_result = button->kv_match_map_ptr[i].tar_result;

if(operator == KV_MATCH_OPERATOR_NULL)
    operand_result = button->key_value;
else if(operator & KV_MATCH_OPERATOR_BITWISE_AND)
    operand_result = (operand_origin & button->key_value);
else if(operator & KV_MATCH_OPERATOR_BITWISE_OR)
    operand_result = (operand_origin | button->key_value);
else if(operator & KV_MATCH_OPERATOR_BITWISE_NOT)
    operand_result = ~(button->key_value);
else if(operator & KV_MATCH_OPERATOR_BITWISE_XOR)
    operand_result = (operand_origin ^ button->key_value);

if(operand_result == tar_result)
{
    button->kv_match_map_ptr[i].kv_func_cb(button);
}
```

- ֧�ֵĲ�������
```c
#define KV_MATCH_OPERATOR_NULL             (0)      // �޲���������ͨ��(key_value == tar_result)?�ж�, Ĭ�������
#define KV_MATCH_OPERATOR_BITWISE_AND      (1 << 0) // ��λ���������(operand & key_value == tar_result)?
#define KV_MATCH_OPERATOR_BITWISE_OR       (1 << 1) // ��λ���������(operand | key_value == tar_result)?
#define KV_MATCH_OPERATOR_BITWISE_NOT      (1 << 2) // ��λȡ����������(~ key_value == tar_result)?
#define KV_MATCH_OPERATOR_BITWISE_XOR      (1 << 2) // ��λ����������(operand ^ key_value == tar_result)?
```

> 4.�����������ʽ���˼·��ÿ���������󵥶���һ�����ݽṹ����

## ? ���ʳ��

### 1��ʹ��
<details>
<summary>���չ��/�۵�C����<img src="https://media.giphy.com/media/WUlplcMpOCEmTGBtBW/giphy.gif" width="30"></summary>

- ��ʹ��callback��ʽΪ����
```c
// 1.����ͷ�ļ�
#include "embedded_button.h"

// 2.���尴��ʵ��
struct button_obj_t button1;

// 3.GPIO��ƽ��ȡ�ӿ�����
uint8_t read_button_pin(uint8_t button_id)
{
    // you can share the GPIO read function with multiple Buttons
    switch(button_id)
    {
        case 0:
            return get_button1_value(); // �û�����ʵ��
            break;

        default:
            return 0;
            break;
    }

    return 0;
}

// 4. ���ü�ֵƥ�����(���ûص��¼�)
void single_click_handle(void* btn)
{
    //do something...
    printf("/****single click****/\r\n");
}

void double_click_handle(void* btn)
{
    //do something...
    printf("/****double click****/\r\n");
}

void long_press_handle(void* btn)
{
    //do something...
    printf("/****long press****/\r\n");
}

void single_click_then_long_press_handle(void* btn)
{
    //do something...
    printf("/****single click and long press****/\r\n");
}

void quintuple_click_handle(void* btn)
{
    //do something...
    if(check_is_repeat_click_mode(btn))
        printf("/****quintuple click****/\r\n");
}

const key_value_match_map_t button1_map[] =
{
    {
        .tar_result = SINGLE_CLICK_KV,
        .kv_func_cb = single_click_handle
    },
    {
        .tar_result = DOUBLE_CLICK_KV,
        .kv_func_cb = double_click_handle
    },
    {
        .tar_result = LONG_PRESEE_START,
        .kv_func_cb = long_press_handle
    },
    {
        .tar_result = SINGLE_CLICK_THEN_LONG_PRESS_KV,
        .kv_func_cb = single_click_then_long_press_handle
    },
    {
        .operand = 0b1010101010,
        .operator = KV_MATCH_OPERATOR_BITWISE_AND,
        .tar_result = 0b1010101010,
        .kv_func_cb = quintuple_click_handle
    }
};
...

int main()
{
/************************************************
****5.��ʼ���������󣬲�������ֱ�Ϊ
****
****- ����ʵ��
****- �󶨰�����GPIO��ƽ��ȡ�ӿ�**read_button1_pin()**
****- ������Ч������ƽ
****- ����ID
****- ��ֵƥ��������ñ�
****- ��ֵƥ��������ñ��С
*************************************************/
    button_init(&button1, read_button_pin, 0, 0, button1_map, ARRAY_SIZE(button1_map));
    // 6.��������
    button_start(&button1);

    // 7. ����һ��5ms����Ķ�ʱ��ѭ�����ð�����̨������ button_ticks()
    __timer_start(button_ticks, 0, 5);

    while(1)
    {}
}
```
![Alt text](image.png)
<br></details>

### 2������

<details>
<summary>���չ��/�۵�<img src="https://media.giphy.com/media/WUlplcMpOCEmTGBtBW/giphy.gif" width="30"></summary>

- ����EB_DEBUG_PRINTF��󽫻Ὺ����ֵ��ӡ���������棬��Ҫ��printf������Ĵ�ӡ������
```c
#define EB_DEBUG_PRINTF printf
```
![alt text](key_value_log.png)
<br></details>

## ? ����
- ����Ŀ���ڱ���ʵ�ʿ�����������һЩ��������ʹ���������⣬��������Ŀ�����ο����ӣ���˼������ϣ������Ĵ˰�������ģ�飬֮ǰ�ᵽ�˱�ģ������ƣ�����˵���д��Ľ��ĵط������ڶఴ��ʱ��ϰ����ı�ʾ��ʽ��Ŀǰ��û���뵽�Ƚ����ŵ�ʵ�ַ�ʽ��������ͷ������һ���Ľ���������һ������󣬸�л����˼���ҵ�С���[shawnfeng0](https://github.com/shawnfeng0)�Լ�����ʹ�ô�ģ���С��飬��ӭһ�𿪷��Ľ���
- ����߼��÷��� [examples](../examples/README.md)

## ? �ο�����
- [MultiButton](https://github.com/0x1abin/MultiButton)
- [FlexibleButton](https://github.com/murphyzhao/FlexibleButton/tree/master)
- [����������FIFO˼��](https://www.armbbs.cn/forum.php?mod=viewthread&tid=111527&highlight=%B0%B4%BC%FC)
