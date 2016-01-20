# Jogo-Allegro
Jogo da Nave Allegro

#include<allegro5/allegro.h>
#include<allegro5/allegro_native_dialog.h>
#include<allegro5/allegro_primitives.h>


int const largura_x = 800;
int const altura_y = 600;

enum TECLAS {CIMA, BAIXO, ESQUERDA, DIREITA};

bool fim = false;
bool nave = false;
bool teclas[] = {false,false,false,false};

int pos_x = 390;
int pos_y = 600;

int main()
{
    ALLEGRO_DISPLAY* display = NULL;
    ALLEGRO_EVENT_QUEUE* eventos = NULL;
    ALLEGRO_TIMER* timer = NULL;

    if(!al_init())
    {
        al_show_native_message_box(NULL, "AVISO!", "ERROR:", "ALLEGRO NÃO PODE SER INICIALIZADO", NULL, ALLEGRO_MESSAGEBOX_ERROR);
        return -1;
    }

    display = al_create_display(largura_x,altura_y);

    if(!display)
    {
        al_show_native_message_box(NULL, "AVISO!", "ERROR:", "O DISPLAY NÃO PODE SER CRIADO!", NULL, ALLEGRO_MESSAGEBOX_ERROR);
        return -1;
    }

// INSTALAÇÕES E INICIALIZAÇÕES
    al_install_keyboard();
    al_install_mouse();
    al_init_primitives_addon();

// FILA DE EVENTOS
    timer = al_create_timer(1.0/50);
    eventos = al_create_event_queue();
    al_register_event_source(eventos, al_get_display_event_source(display));
    al_register_event_source(eventos, al_get_keyboard_event_source());
    al_register_event_source(eventos, al_get_mouse_event_source());
    al_register_event_source(eventos, al_get_timer_event_source(timer));

// LOOP PRINCIAPL

    al_start_timer(timer);

    while(!fim)
    {
        ALLEGRO_EVENT ev;
        al_wait_for_event(eventos, &ev);

        if(ev.type == ALLEGRO_EVENT_DISPLAY_CLOSE)
            fim = true;
        else if(ev.type == ALLEGRO_EVENT_KEY_DOWN)
        {
            if(ev.keyboard.keycode == ALLEGRO_KEY_ESCAPE)
                fim = true;

            switch(ev.keyboard.keycode)
            {
            case ALLEGRO_KEY_UP:
                teclas[CIMA] = true;
                break;
            case ALLEGRO_KEY_DOWN:
                teclas[BAIXO] = true;
                break;
            case ALLEGRO_KEY_LEFT:
                teclas[ESQUERDA] = true;
                break;
            case ALLEGRO_KEY_RIGHT:
                teclas[DIREITA] = true;
                break;
            }

        }

        else if(ev.type == ALLEGRO_EVENT_KEY_UP)
        {
            switch(ev.keyboard.keycode)
            {
            case ALLEGRO_KEY_UP:
                teclas[CIMA] = false;
                break;
            case ALLEGRO_KEY_DOWN:
                teclas[BAIXO] = false;
                break;
            case ALLEGRO_KEY_LEFT:
                teclas[ESQUERDA] = false;
                break;
            case ALLEGRO_KEY_RIGHT:
                teclas[DIREITA] = false;
                break;
            }

        }

        else if(ev.type == ALLEGRO_EVENT_TIMER)
        {
            if(teclas[CIMA] && pos_y >= 80)
                pos_y -= teclas[CIMA]*5;
            else if(teclas[BAIXO] && pos_y <= 610)
                pos_y += teclas[BAIXO]*5;
            if(teclas[ESQUERDA] && pos_x >= 10)
                pos_x -= teclas[ESQUERDA]*5;
            else if(teclas[DIREITA] && pos_x <= 740)
                pos_x += teclas[DIREITA]*5;
            nave = true;


        }



        if(nave && al_is_event_queue_empty(eventos))
        {
            al_draw_filled_rectangle(pos_x+35,pos_y-70,pos_x+40,pos_y-20,al_map_rgb(255,0,0));
            al_draw_filled_rectangle(pos_x+10,pos_y-70,pos_x+15,pos_y-20,al_map_rgb(255,0,0));
            al_draw_filled_triangle(pos_x,pos_y-20,pos_x+25,pos_y-70,pos_x+50,pos_y-20,al_map_rgb(0,0,255));
            nave = false;
            al_flip_display();
            al_clear_to_color(al_map_rgb(0,0,0));
        }


    }

    al_destroy_display(display);
    al_destroy_event_queue(eventos);
    al_destroy_timer(timer);
    return 0;
}

