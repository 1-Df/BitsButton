# EmbeddedButton
## ���
EmbeddedButton��һ�������������õ�Ƕ��ʽ��������ģ�飬��������չ������֧�ֶ��������������̰������ȶ��ְ����¼�����ģ��ͨ���첽�ص���ʽ���򻯳���ṹ�����ݼ�����ԭ����������������߼���֧�š�

## ʹ�÷���
1.���尴��ʵ��

```c
struct button_obj_t button1;
```
2.������ֵӳ���(���ûص��¼�)

```c
const key_value_map_t button1_map[] =
{
    {
        .key_value = SINGLE_CLICK_KV,
        .kv_func_cb = single_press_handle
    },
    {
        .key_value = LONG_PRESEE_START,
        .kv_func_cb = long_press_handle
    },
    {
        .key_value = SINGLE_CLICK_THEN_LONG_PRESS_KV,
        .kv_func_cb = single_press_then_long_press_handle
    },
};
```
3.��ʼ���������󣬲�������ֱ�Ϊ

- ����ʵ��
- �󶨰�����GPIO��ƽ��ȡ�ӿ�**read_button1_pin()**
- ������Ч������ƽ
- ����ID
- ��ֵӳ���
- ��ֵӳ����С
```c
button_init(&button1, read_button1_pin, 0, 0, button1_map, ARRAY_SIZE(button1_map));
```

4.��������

```c
button_start(&button1);
```

5.����һ��5ms����Ķ�ʱ��ѭ�����ð�����̨������

```c
while(1) {
    ...
    if(timer_ticks == 5) {
        timer_ticks = 0;

        button_ticks();
    }
}
```

## ����

> 1.�����򵥼���ԭ��֧�������������ж��߼�
- ֻҪ��ֵ���㣬ʱ��tick++
- ֻҪ����״̬�����仯���ı�һ�μ�ֵ��**__append_bit()**����tickʱ�����㣨ȷ��tickΪ���»�̧���ʱ�䣩
- ��tickʱ��ĳ��̼�����̧����Ϊһ��״̬�������ж����ݣ����Ժܺõ�ʵ�ֶ̰������Ȳ�����

> 2.ʹ��C����ʵ�֣���������λ������ʵ��ÿ��������ֵ�Ķ����Ƽ�¼��ʾ��1�����£�0�����ɿ�

��ֵ | ˵��
--- | ---
0b0 | δ����
0b010 | ��һ�ε����¼�
0b01010 | ˫��
0b01010...n | n����
0b011 | ������ʼ�¼�
0b0111| ���������¼�
0b01110|���������¼�
0b01011|�̰������¼�
0b0101011 | ˫�������¼�
0b01010..n11 | n���������¼�

> 3.������������˼����ɶ�Ӧ�����¼��ĵ��ã�
```c
typedef struct {
    key_value_type_t key_value;
    void (*kv_func_cb)(void*);
} key_value_map_t;

const key_value_map_t button1_map[] =
{
    {
        .key_value = SINGLE_CLICK_KV,
        .kv_func_cb = single_press_handle
    },
    {
        .key_value = LONG_PRESEE_START,
        .kv_func_cb = long_press_handle
    },
    {
        .key_value = SINGLE_CLICK_THEN_LONG_PRESS_KV,
        .kv_func_cb = single_press_then_long_press_handle
    },
};

for(size_t i = 0; i < button->map_size; i++) {
    if((button->map_ptr[i].key_value == button->key_value)
    && (button->map_ptr[i].kv_func_cb))
    {
        button->map_ptr[i].kv_func_cb(button);
    }
}
```

> 4.�����������ʽ���˼·��ÿ���������󵥶���һ�����ݽṹ����

```c
typedef struct button_obj_t {
    uint8_t  debounce_cnt : 4;
    uint8_t  active_level : 1;
    uint8_t  read_level : 1;
    uint8_t  read_level_update : 1;
    uint8_t  event_analyze_en : 1;
    uint8_t  id;
    uint16_t ticks;
    state_bits_type_t state_bits;
    key_value_type_t key_value;
    uint8_t  (*_read_button_func_ptr)(uint8_t button_id_);
    const key_value_map_t *map_ptr;
    size_t map_size;
    struct button_obj_t* next;
}button_obj_t;
```

## Examples

```c
#include "embedded_button.h"

struct button_obj_t button1;

uint8_t read_button_pin(uint8_t button_id)
{
    // you can share the GPIO read function with multiple Buttons
    switch(button_id)
    {
        case 0:
            return get_button1_value(); //Require self implementation
            break;

        default:
            return 0;
            break;
    }

    return 0;
}

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

void double_click_then_long_press_handle(void* btn)
{
    //do something...
    printf("/****double click and long press****/\r\n");
}

const key_value_map_t button1_map[] =
{
    {
        .key_value = SINGLE_CLICK_KV,
        .kv_func_cb = single_click_handle
    },
    {
        .key_value = DOUBLE_CLICK_KV,
        .kv_func_cb = double_click_handle
    },
    {
        .key_value = LONG_PRESEE_START,
        .kv_func_cb = long_press_handle
    },
    {
        .key_value = SINGLE_CLICK_THEN_LONG_PRESS_KV,
        .kv_func_cb = single_click_then_long_press_handle
    },
    {
        .key_value = DOUBLE_CLICK_THEN_LONG_PRESS_KV,
        .kv_func_cb = double_click_then_long_press_handle
    }
};
...

int main()
{
    button_init(&button1, read_button_pin, 0, 0, button1_map, ARRAY_SIZE(button1_map));
    button_start(&button1);

    //make the timer invoking the button_ticks() interval 5ms.
    //This function is implemented by yourself.
    __timer_start(button_ticks, 0, 5);

    while(1)
    {}
}
```
![Alt text](image.png)

## ����
- ����Ŀ���ڱ���ʵ�ʿ�����������һЩ��������ʹ���������⣬��������Ŀ�����ο����ӣ���˼������ϣ������Ĵ˰�������ģ�飬֮ǰ�ᵽ�˱�ģ������ƣ�����˵���д��Ľ��ĵط������ڶఴ��ʱ��ϰ����ı�ʾ��ʽ��Ŀǰ��û���뵽�Ƚ����ŵ�ʵ�ַ�ʽ��������ͷ������һ���Ľ���������һ������󣬸�л����˼���ҵ�С���[shawnfeng0](https://github.com/shawnfeng0)�Լ�����ʹ�ô�ģ���С��飬��ӭһ�𿪷��Ľ���

## �ο�����
- [MultiButton](https://github.com/0x1abin/MultiButton)
- [FlexibleButton](https://github.com/murphyzhao/FlexibleButton/tree/master)
- [����������FIFO˼��](https://www.armbbs.cn/forum.php?mod=viewthread&tid=111527&highlight=%B0%B4%BC%FC)