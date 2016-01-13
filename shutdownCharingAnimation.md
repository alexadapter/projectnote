# 关机充电界面


### 存放路径: /system/core/charger


> 原理:根据充电状态来调整动画..
 * power.c里面提供了系统电量状态文件路径..可以到指定文件里面读取状态
 * charger.c提供动画实现过程.主要修改update_screen_state和redraw_screen即可

### 相关资源 /bootable/recovery/minui

> 添加字体的方法
 * 添加fontxx.h[字体](chargerFonts.md)文件到minui路径下 
 * graphics.c提供了ui的绘制函数..不改动系统原先字体大小就添加gr_init_fontNew_extend()和gr_init_extend()..*复制于原先的gr_init和gr_init_font*
 
 ```c 
 static void gr_init_font_extend(void)
{
    gr_font = calloc(sizeof(*gr_font), 1);


    int res = res_create_surface("font", (void**)&(gr_font->texture));
    if (res == 0) {
        // The font image should be a 96x2 array of character images.  The
        // columns are the printable ASCII characters 0x20 - 0x7f.  The
        // top row is regular text; the bottom row is bold.
        gr_font->cwidth = gr_font->texture->width / 96;
        gr_font->cheight = gr_font->texture->height / 2;
    } else {
        printf("failed to read font: res=%d\n", res);


        // fall back to the compiled-in font.
        gr_font->texture = malloc(sizeof(*gr_font->texture));
        gr_font->texture->width = fonten76_65x85.width;
        gr_font->texture->height = fonten76_65x85.height;
        gr_font->texture->stride = fonten76_65x85.width;


        unsigned char* bits = malloc(fonten76_65x85.width * fonten76_65x85.height);
        gr_font->texture->data = (void*) bits;


        unsigned char data;
        unsigned char* in = fonten76_65x85.rundata;
        while((data = *in++)) {
            memset(bits, (data & 0x80) ? 255 : 0, data & 0x7f);
            bits += (data & 0x7f);
        }


        gr_font->cwidth = fonten76_65x85.cwidth;
        gr_font->cheight = fonten76_65x85.cheight;
    }


    // interpret the grayscale as alpha
    gr_font->texture->format = GGL_PIXEL_FORMAT_A_8;
}

int gr_init_extend(void){
    .....
    //gr_init_font();
    gr_init_font_extend();
    .....
}
```

