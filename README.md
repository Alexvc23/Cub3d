# Cub3d
Raycating project
- To execute this project, clone the public repository, and inside the folder execute, the next bash command:
    
   `make && ./Cub3d maps/map_std.cub`
   
   
![](https://github.com/Alexvc23/Cub3d/blob/3ef63cee979732bd98a2a66956119d53bc22f037/cub3d.gif)


- Resources
    - In order to develope this project we need to use the minilibx library, below all the explanations and function prototypes:
    
    [MiniLibX](https://harm-smits.github.io/42docs/libs/minilibx)
    
    [https://harm-smits.github.io/42docs/libs/minilibx](https://harm-smits.github.io/42docs/libs/minilibx)
    
- How to manipulate colors in `C` using `minilibX`
    - Code `examples`
        
        ```c
        /* 
        The next three functions return each r g b color individually
        */
        int	get_r(int trgb)
        {
        	return ((trgb >> 16) & 0xFF);
        }
        
        int	get_g(int trgb)
        {
        	return ((trgb >> 8) & 0xFF);
        }
        
        int	get_b(int trgb)
        {
        	return (trgb & 0xFF);
        }
        
        /* 
        return the int representation of rgb 
         */
        int rgb_to_int(int r, int g, int b)
        {
        	return ((r<<16) + (g<<8) + b);
        }
        
        /* 
        add shadow to any number by modifying each r g b value
        */
        int	add_shadow(int rgb)
        {
            int r;
            int g;
            int b;
        
            r = get_r(rgb);
            b = get_g(rgb);
            g = get_b(rgb);
            return (rgb_to_int(r * 0.5, g * 0.5 , b * 0.5));
        }
        ```
        
        - Result applying `add_shade` to red
            
            ![Screenshot from 2022-08-02 14-59-35.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/06728b16-84ce-4325-b2fb-7a724f206fdc/Screenshot_from_2022-08-02_14-59-35.png)
            
    - `Resources`
        
        [Colors](https://harm-smits.github.io/42docs/libs/minilibx/colors.html)
        
- Project developement
    
    **Cub3d raycasting**
    
     This project was made with the colaboration of **Elsruora who** did the parsing part**,** the library used to create this project is called minilibx is provided by school 42, you can find the reference above.
    
    - To understand this project and find all the tecniques used to modelate a raycasting, follow the next video series.
    - Video
        
        [Make Your Own Raycaster Part 1](https://www.youtube.com/watch?v=gYRrGTC7GtA&t=863s)
        
    
    ### *If you want to know to how to draw a line  to representate the rays,  in C, using minilibx  library, see below code*
    
    - Code
        
        ```c
        /* ************************************************************************** */
        /*                                                                            */
        /*                                                        :::      ::::::::   */
        /*   draw_line.c                                        :+:      :+:    :+:   */
        /*                                                    +:+ +:+         +:+     */
        /*   By: jvalenci <jvalenci@student.42.fr>          +#+  +:+       +#+        */
        /*                                                +#+#+#+#+#+   +#+           */
        /*   Created: 2022/07/16 10:32:44 by jvalenci          #+#    #+#             */
        /*   Updated: 2022/09/24 13:08:52 by jvalenci         ###   ########.fr       */
        /*                                                                            */
        /* ************************************************************************** */
        
        #include "../../includes/cub3d.h"
        
        void	plot_line_low(t_map *m, float *xy0, float *xy1)
        {
        	m->l->dx = xy1[0] - xy0[0];
        	m->l->dy = xy1[1] - xy0[1];
        	m->l->yi = 1;
        	if (m->l->dy < 0)
        	{
        		m->l->yi = -1;
        		m->l->dy *= -1;
        	}
        	m->l->d = (2 * m->l->dy) - m->l->dx;
        	m->l->x = xy0[0];
        	m->l->y = xy0[1];
        	while (m->l->x < xy1[0])
        	{
        		my_mlx_pixel_put(m->s_img[0], m->l->x, m->l->y, \
        				choose_color(m, m->t->wall_code));
        		if (m->l->d > 0)
        		{
        			m->l->y += m->l->yi;
        			m->l->d += (2 * (m->l->dy - m->l->dx));
        		}
        		else
        			m->l->d += (2 * m->l->dy);
        		m->l->x++;
        	}
        }
        
        void	plot_line_hight(t_map *m, float *xy0, float *xy1)
        {
        	m->l->dx = xy1[0] - xy0[0];
        	m->l->dy = xy1[1] - xy0[1];
        	m->l->xi = 1;
        	if (m->l->dx < 0)
        	{
        		m->l->xi = -1;
        		m->l->dx *= -1;
        	}
        	m->l->d = (2 * m->l->dx) - m->l->dy;
        	m->l->y = xy0[1];
        	m->l->x = xy0[0];
        	while (m->l->y < xy1[1])
        	{
        				// choose_color(m, m->t->wall_code));
                my_mlx_pixel_put(m->s_img[0], m->l->x, m->l->y, \
                        choose_color(m, m->t->wall_code));
        		if (m->l->d > 0)
        		{
        			m->l->x += m->l->xi;
        			m->l->d += (2 * (m->l->dx - m->l->dy));
        		}
        		else
        			m->l->d += (2 * m->l->dx);
        		m->l->y++;
        	}
        }
        
        /**
           @brief Bresenham's line algorithm is a line drawing algorithm that determines
        		   the points of an n-dimensional raster that should be selected in order
        		   to form a close approximation to a straight line between two points.
        		   It is commonly used to draw line primitives in a bitmap image
        
           --->    https://en.wikipedia.org/wiki/Bresenham%27s_line_algorithm
        */
        void	plot_line(t_map *m, float x0, float y0)
        {
        	float	xy0[2];
        
        	xy0[0] = x0;
        	xy0[1] = y0;
        	if (m->height <= 448 && m->width <= 1024)
        	{
        		if (abs((int)m->l->pdxy[1] - (int)xy0[1]) < \
        				abs((int)m->l->pdxy[0] - (int)xy0[0]))
        		{
        			if (xy0[0] > m->l->pdxy[0])
        				plot_line_low(m, m->l->pdxy, xy0);
        			else
        				plot_line_low(m, xy0, m->l->pdxy);
        		}
        		else
        		{
        			if (xy0[1] > m->l->pdxy[1])
        				plot_line_hight(m, m->l->pdxy, xy0);
        			else
        				plot_line_hight(m, xy0, m->l->pdxy);
        		}
        	}
        }
        ```
        
    
    
